+++
title = 'CMU_cs251'
date = 2024-12-20T20:40:18+08:00
draft = true
+++

## Part 1 Introduction

## Part 2 Mathmatical Reasoning and Proof

**Old regular mathematics**

Follow your **intuition** while exploring mathematics.

Mathematical reasoning: axiom --(deduction)--> truths(which is limited to one's interpretation) --> truths. Euclidean geometry is kinda a paradigm.

Can every mathematical theorem be **derived** from a set of agreed upon **axioms**? Similar to breaking down any program to pure machine codes(machine codes are like axiom here).

A naive definition of a set breaks mathematics.

How do we formalize **mathematical reasoning** by building a mathmatical model? How do we formally represent **statements**? Which statements are "obviously" true?(What are the **axioms**) Which **decuction rules** are allowed? How are they formally represented?

Mathematical reasoning is **computation**.

1. **Aristotle**: logic is about **unambiguous statements**, **deductive reasoning** and **first principle approach**.
2. **Euclid**: A mathematical incarnation of Aristolean logic.
3. **Leibniz**: Envisioned an algebra/calculus for logic(computational propositional logic).
4. **George Boole**: Inventor of propositional calculus.
5. **Georg Cantor**: Father of **set theory**. The person who dared to tackle infinity head-on.
6. **Gottlob Frege**: Lays the foundation for **first order logic**(predicate calculus). Proposes axioms for set theory.
7. **Bertrand Russell**: Barber paradox.
8. **Whitehead**: *Principia Mathematica, Volume 2*
9. **David Hilbert**: 
10. **kurt Godel**: **Completeness theorem** and **incompleteness theorem**.
11. **Alan Turing**: 

The upshot: we can rigoously formalize mathematical proofs while there are limits to what can be proved.

## Part 3 Strings, encodings, problems

- If you want to understand something better, one of the best method, the most reliable method we have is, mathematical modelling.

Another arogant claim from the logic erocted western. I am quit dissapoint with that.

Input --> "computer" --> output 

Information measures the number of possible state in which the system can be. Information is zero if the system only have one state. 

Imagine computing as a process of information, which takes some input and then produces some output. Now we need a way to represent those information of input and output.

If we human-beings are in the position of the computer, the language we use to communicate is the representation of  the information we are exchanging. 

**encoding**: given a set A of objects, an encoding of elements of A is an **injective function**. Enc: A --> $/sum_{}^{}*$ 
It is kind of like **labling** each one of the element.

Does every object have a corresponding encoding?
Can two objects have the same encoding?
Does every string correspond to a valid encoding?

Which sets are encodable? 
Encodability = Countability (?)

For the uncountable sets, e.g. rational number, we try to represent them by approximating with certain precision.

What is **computation**? What is an **algorithm**? How can we mathematically define them?

Reasonable assumptions:
- Computer is deterministic(Give a certain input, the computer always returns the same output)
- Input can be any finite-length string
- For all inputs, there is an output
- Output is a finite-length string

How can we characterize the input/output behavior of a computer? 

- Function.

However, computation is about **how** instead of **what**.

A computer/algorithm **solves** function prblem **f** if its input/output behavior corresponds to **f**.

**Language**: Any set **L** of finite-length strings over an alphabet $/sum_{}^{} $.

There  is a one-to-one crrespondence between **decision problems** and **language**.

Integer factorization problem can convert to its decision version. Vise versa. OK, we finally get to the place of **Turing machine**.

Are all languages computable/decidable?
How can you prove a language is undecidable?
how do ew measure the complexity of algorithms deciding languages?
How do we classify languages according to the resources needed to decide them?
P ?= NP

### Summary

We human-beings use strings to communicate. And encoding just represent the **strings** in another form, for the convenience of mathematical "**computer**"
(it is just a model/representation/concept instead of the exact computer we use today). The key part, in my opinion, is that 

- **language is a subset of alphabet**. 
- A computer/algorithm **solves** function problem **f** if its input/output behavior corresponds to **f** and integer factorization problem can convert to decision version.

Now we can feed any problem to a customed computer(or say, function), encoding input and output(if needed), and get the result. 
We are able to know if a problem is solvable since we could convert any problem to its decision version(id.est. the output is "**true**" or "**false**".)
Up to this moment, this course give me a sence of the divergence of **Turing Machine**.

For the **language** part, I don't know why he brought them here. I myself try to learn from those videos after a fail attempt to understand the parser 
part of the book *Craftering Interpreters* and these have some underline correspondece(I mean I would have no idea why the **language** part is related 
to computation if I wasn't have the preknowledge/confusion).

And by defining a mapping between the alphabet and {0,1}, we could create any language.

## Deterministic Finite Automata Part 1

We gonna talk about **how** to get the output from the input.

**Terminology**: 

- **Computational Model**: Allowed rulses for **information processing**.
- **Machine = Computer = Program = Algorithm**: An instantiation of the computational model.(a specific sequence of information processing)

**Assumptions**:

1. No "universal machines"(we haven't have such concept yet)
2. We only care about **decision problems**.

##### Deterministic Finite Automata

A restricted model of computation:

- limited memory
- reads input from left to right, and **accepts** or **rejects**

