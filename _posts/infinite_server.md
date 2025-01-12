+++
title = 'Infinite_server, a rough toy'
date = 2024-03-16T11:59:52+08:00
draft = false
+++

### Infinite server

[Project address](https://gitlab.com/hzxie/infinite-server)

#### How does it work

In normal case, [PHP-FPM](https://www.php.net/manual/en/install.fpm.php) runs the php script forwarded by **Nginx** or **Apache** which would show the frontend when we visit the dashboard in our browser. How does the dashboard node get the status of its client nodes? There are two kinds of methods.

##### Pull method

The dashboard node sends requests to the client node. The client uses **Nginx**/**Apache** to forward the requests of php script to PHP-FPM that would run the scripts which get the status information about their local machine.

##### Push method

The client runs the php script to get information about its local machine and send the result to the dashboard node.

#### Analysis

##### Dashboard node

###### Nginx configuration

```
location / {
     proxy_http_version          1.1;
     proxy_buffering             off;
     try_files      $uri   /infinite-server/index.php$is_args$args;
}
location ~ \.php$ {
     include snippets/fastcgi-php.conf;
     fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
}
```

`try_files uri /infinite-server/index.phpis_args$args;`  Tries to serve the requested URI directly, and if that fails, it rewrites the request to `/infinite-server/index.php` with any query parameters (`$is_args$args`) appended. And according to `location ~ .php$ {}`, any request ending in `.php` would be processed by **php8.2-fpm**. 

Attension: The configuration file of php8,2-fpm.sock is `/etc/php/8.2/fpm/pool.d/www.conf`. If we install the php-fpm running `apt install php-fpm`, the default path of php-fpm.sock is `/run/php/php-fpm.sock`. We need to modify it in the configuration file.

###### The mechanism of  the project

The request would be rewrited to `/infinite-server/index.php` when we visit `http://ip/infinite-server`. 

```php
// infinite-server/index.php
include_once("api/index.php");
```

In **pull method**, `infinite-server/index.php` contains another php file, which is the key script of this project. In `infinite-server/api/index.php`, `case "/"` would return the `infinite-server/default.html` to the frontend. There are some javascript codes in `infinite-server/default.html` that would send request with path of `./servers` and `./status` to the dashboard node. After receiving those requests from the browser,  `case "/servers"` and `case "/status"` in `infinite-server/api/index.php` would send request to client to get their status(the client node should put `status.php` in `/var/www/html/`). So the client nodes must set up a web server and PHP environment to process the requests.  The `case "/push"` is to deal with the information of client nodes by **push method**. In **push method**, the client nodes run `status.php` automatically to sending information to the dashboard node. The configuration file in the dashboard node should contain the same name and token as the one of the  corresponding client node. 

###### Details

```php
// infinite-server/api/index.php
// push method
if (!array_key_exists($name, $config["servers"]) ||
    $config["servers"][$name]["url"] != $token) {
    http_response_code(403);
    break;
    }
```

These codes compares the **node_name** and **token** from client node with those of the dashboard node. 

```json
{
    "name": "node_name",
    "token": "TOKEN",
    "url": "http://dashboard_node_ip/infinite-server/push"
}
```

In the configuration file of the **client node**, **node_name**, **token** and **dashboard_node_ip** should be contained. 

```json
"servers": {
        //pull method
        "Awesome Server": {
            "region": "HK",
            "url": "http://localhost/status.php"
        },
        //push method
        "Push Server": {
            "region": "SG",
            "url": "RAMDOM-TOKEN"
        }
    }
```

In the configuration file of the dashboard node, **node_name** and **url**(when implement **pull** method, its value should be the form of `http://client_node_ip/status.php`; when implement **push** method, its value  should be a token) should be contained.

```php
//infinite-server/api/helper.php
function sse_loop($cfg) {
    header("Cache-Control: no-store");
    header("Content-Type: text/event-stream");
    header('X-Accel-Buffering: no');

    // The event loop
    while (true) {
        echo 'data: ' . json_encode(get_server_status($cfg)) . "\n\n";
        ob_end_flush();
        flush(); 
        // Break the loop if the client aborted the connection (closed the page)
        if (connection_aborted()) {
            break;
        }
        sleep($cfg["interval"]);
    }
}
```

**Bug**: when we close the page in our browser, **connection_aborted()** could not detect the disconnection. And then the `while` loop would keep running continuously. And I need restart nginx to terminate it. 

**A rough solution**: embed some codes of a timer to terminate this while loop. For example, hard break it after  30 seconds. 

###### More details

A interesting conversation:
**Me**: For this configuration(as above) of nginx, when I visit http://ip/infinite-server/push, what would happen?
**LLM**: According to this configuration file, when you visit http://ip/infinite-server/push, Nginx will attempt to locate the index.php file under the /infinite-server directory. If found, it will use FastCGI to pass the request to the PHP interpreter for processing, and the PHP interpreter will pass the request to the index.php file using the $args parameter. If the index.php file is not found, Nginx will return a 404 Not Found error.

**Me**: If index.php contains such codes(as the same as the one in the project), what would happen?

**LLM**: This index.php file is a simple PHP application that performs different operations based on the requested path. In your case, when you access http://ip/infinite-server/push, Nginx will pass the request to this index.php file for processing. Based on the code in index.php, it will check the name and token parameters in the request and process the request based on the values of these parameters. Specifically, it will validate if the name parameter exists in the server list in the configuration file and if its corresponding URL matches the token parameter. If validation passes, it will perform different actions based on whether the time parameter is present in the request, calling either dump_server_status or dump_server_info functions to handle server status information. If validation fails or the requested path does not match any of the above cases, it will return a 403 Forbidden or 404 Not Found error.
**Me**: How does index.php match case "/push"? 

**LLM**: The request path matching in index.php is achieved through the $request_path variable, which is obtained by parsing ￥_SERVER["REQUEST_URI"]. When you access http://ip/infinite-server/push, the value of ￥_SERVER["REQUEST_URI"] is "/infinite-server/push". Therefore, after parsing, the value of ￥_request_path is "/push". Subsequently, the switch statement match the value of ￥request_path and finds the corresponding case for "/push", executing the code within it.

(Replace the '￥‘ with ’$')
