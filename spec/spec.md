# Step-By-Step Markup Language (SBSML)

## Abstract

SBSML is a human readable, quickly writable, [descriptive](https://en.wikipedia.org/wiki/Markup_language#:~:text=in%20many%20places.-,Descriptive%20markup,-%5Bedit%5D)
markup language for  documenting system or business processes.

## Introduction

SBSML seeks to solve the common problem of describing a process - usually technical - in as few total words
as possible and with a minimum amount of markup specific attributes like tags or annotations, in a way that can be 
both human and machine readable. 

We consider a *process* to be any series of *steps* that start at a single node, which can include *conditional* logic
as well as jumps between multiple other *processes*.

In English, such a thing is usually represented using a numbered list, like the below example for making a cake;

1. Add the Packet mix
2. Add the milk
3. Whip the mix
4. Bake for 20 minutes

But what if we want to describe how to make the icing, or what happens if your oven catches
fire? There's no universally accepted standard for this sort of procedure, and this is what this spec is designed to
solve.

Describing the above, including those cases, in SBSML would look like this;

```
--- Making the Cake ---
1. Add the packet mix
2. Add the milk
3. Whip the mix
4. Bake for 20 minutes
IF: Oven too hot!
DO: Turn Oven Down
IF: Oven too cold!
DO: Turn oven up
THEN: Make the icing 

-- Turn Oven Down --
1. Turn the temperature dial anti-clockwise

-- Turn oven up --
1. Turn the temperature dial clockwise

-- Make the icing --
1. Add the icing
2. Melt some butter
3. Add the butter
4. Mix together
5. Whip and add to cake 
```

# SBSML Nodes

## Step

the most basic node type in SBSML. A step node is a single step in a parent *process*.

A basic step node;

`1. Do the first step`

Step nodes support several annotations. 

You can signal that a step produces a specific output with the *output* annotation `>`

`1. Melt some butter > melted butter`

Likewise a step can reference input using the *input* annotation `>>`

`1. Butter >> Melt some butter > melted butter`

Steps can also have *substeps* or *nested processes*. This is done by providing an non-numerical character after the 
step number.

```
1. Melt some butter
 1a. Transfer the melted butter to a dish
```

## Process

Declares the start of a *process*. A process is an ordered collection of *step* nodes. 

A process node is created using the *process declaration tags* `---`

Processes can simply be a name declaration.

```
--- A basic process name --
```

Like *steps*, a *process* can have both inputs and outputs. These must be declared around the *process* declaration. 

```
>> Unmelted butter
--- Melt some Butter ---
> Melted Butter
1. Melt the butter
```

## Conditional

Used to provide logical branching of a process. A *conditional* node is made up of two components; the *condition*,
declared with `IF:<condition>` and the *next node*, declared with `THEN:` 

```
IF: butter not melted
THEN: Melt the butter
```

Conditional nodes can follow any *step* node. The *next node* declaration can either be a reference to a step in the 
current process, or another process. 



# Annotations

### Input/Output

Any node can use *input* `>>` and *output* `>` annotations to describe parameters to a step. Both allow multiples via
comma seperation of the values, for example, here is a step with two inputs and one output;

```
1. butter, microwave >> Melt the butter > Melted butter
```

### Node References

Every node in an SBSML document belongs to a parent, or to the document itself. Therefore, it's possible to reference
any specific step or sub-step in the document usng the *reference* annotation format. This can be used to jump 
specifically to any other step in the document.

The node reference format is `[<process name>:<step number>?]`

Reference a specific process step

```
[Melt some butter:1]
```

Reference a process:

```
[Melt some butter]
```

# Type Declarations

You can use *type* declarations to define items that may be used multiple times in the same process or to describe some
component used in the process in more detail.

Type declarations should occur before their first reference in the document and are specified via their name 
enclosed in tildes; `~`

```
~ Microwave ~
A microwave is an electronic cooking tool that can rapidly heat food.

-- Melt the buter -- 
1. Put the butter in the Microwave
```

# Description

Each node supports a *description* of any length as a body of text directly after the node it describes.

```
1. Melt the butter
Room temperature or slightly melted butter whips easier and improves the texture of the icing.

Don't overmelt it!

2. Transfer butter to plate
```
