---
id: introduction
title: Polynomial Identity Language
sidebar_label: Introduction
description: The concept of Polynomial Identity Language
keywords:
  - docs
  - polygon
  - PIL
  - state
  - machine
  - Polygon zkEVM
image: https://wiki.polygon.technology/img/thumbnail/polygon-zkevm.png
---

## Introduction

**Polynomial Identity Language (PIL)** is a novel domain-specific language useful for defining state machines. The aim for creating PIL is to provide developers a holistic framework for both constructing state machines through an easy-to-use interface, and abstracting the complexity of the proving mechanisms.

One of the main peculiarities of PIL is its **modularity**, which allows programmers to define parametrizable state machines, called $\texttt{namespaces}$, which can be instantiated from larger state machines. Building state machines in a modular manner makes it easier to test, review, audit and formally verify even large and complex state machines. In this regard, by using PIL, developers can create their own custom namespaces or instantiate namespaces from some public library.

Some of the keys features of PIL are;

- Providing $\texttt{namespaces}$ for naming the essential parts that constitutes state machines,
- Denoting whether the polynomials are $\texttt{committed}$ or $\texttt{constant}$,
- Expressing polynomial relations, including $\texttt{identities}$ and $\texttt{lookup arguments}$, and
- Specifying the type of a polynomial, such as $\texttt{bool}$ or $\texttt{u32}$.

## State Machines: The Computational Model Behind PIL

Many other domain-specific languages (DSL) or toolstacks, such as [Circom](https://docs.circom.io/) or [Halo2](https://zcash.github.io/halo2/), focus on the abstraction of a particular computational model, such as an arithmetic circuit.

Arithmetic circuits arise naturally in the context of succinct interactive protocols and are therefore an appropriate representation in the context of PIL. These circuits are covered by developer tools generally in two ways, either in the vanilla **PlonK Style** or the **PlonKish Style**. Check out the below diagram for a high-level description of these two styles and how they differ.

![Vanilla Plonk vs PlonKish Circuit Representation Style](figures/fig1-plnk-plnkish.png)

However, recent proof systems such as STARKs have shown that arithmetic circuits might not be the best computational models in all use-cases.

Given a complete programming language, computing a valid proof for a circuit satisfiability problem, may result in long proving times due to the overhead of re-used logic. Opting for deployment of state machines, with their low-level programming, shorter proving times are attainable especially with the advent of proof / verification-aiding languages such as PIL.

A typical state machine takes some input and produces the corresponding output, according to the Arithmetic-Logic Unit (ALU). This ALU is the very core of the state machine as it determines the internal state of the state machine, as well as the values of its output. Below diagram provides a high-level description of a state machine architecture. 

![Architectural view of a State Machine](figures/fig2-alu-3states.png)

### Comparing Circuit and State Machines

1. The diagram below shows the comparison between the Circuit and State Machine in a loop-based computation program.

  ![Circuit and state machine comparison in a loop-based computation](figures/fig3-crct-sm.png)

2. The diagram below shows the comparison between the Circuit and State Machine in a branch-based computation program.
  ![Circuit and State Machine comparison in a branch-based computation](figures/fig4-arth-crct-sm.png)
