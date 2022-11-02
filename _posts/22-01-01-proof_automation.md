---
title: Following Along with UIUC's CS 598 PROOF AUTOMATION
date: 2022-01-03
categories: [Proof Automation]
tags: [verification, proof assistants]     # TAG names should always be lowercase
---

[Dr. Talia Ringer](https://dependenttyp.es/) is teaching a [course on proof automation](https://dependenttyp.es/classes/598sp2022.html) at the University of Illinois at Urbana-Champaign. I've been a programming language nerd since dabbling with Haskell and the like in undergrad, and I'm very interested in the whole area of "proving things about computation". Naturally, this falls squarely into that intersection, so I'm going to be following along with the course as best as I can manage this semester. This ongoing blog post will be where I post my notes, thoughts, and some solutions (only the non-directly-copyable sort).

If you're interested in a discussion on anything in this post, please reply to my [relevant tweet](https://twitter.com/EtherealEq/status/1478588113601679360).

## Homework 1

Chapters 1 and 3 of QED at Large: A Survey of Engineering of Formally Verified Software by Talia Ringer, Karl Palmskog, Ilya Sergey, Milos Gligoric, and Zachary Tatlock.

#### Reading Thoughts:
The reading assignment served as a good introduction to the history of interactive verification. I think would have liked some companion pieces on the real and potential broader impacts of verification in the world. That would have felt more complete in an "end-to-end" sense and helped me get excited about the topic.

I have one question about the paper: what is the difference between program verifiers and the application of proof assistants to verifying programs? Section 1.2 of the paper discusses its scope, and notes that it will focus on the latter, but not the former. I'm unsure of the relationship between the topics specifically omitted from the scope, i.e. program verifiers, theorem provers, and constraint solvers, and the topic of focus, proof assistants in the context of software engineering.

#### Homework Prompt:
Section 3.3 lists exactly one machine learning system that has been verified in a proof assistant. Based on what you know so far, do you think there is much hope for verifying state-of-the-art machine learning systems inside of a proof assistant? If so, why and how? And if not, what do you think it would take to be able to formally verify state-of-the-art machine learning systems?

#### Response:
In short, if "state-of-the-art" refers to the darlings of the machine learning research community, I don't think they will be touched by formal verification in any meaningful way for a long time. On the other hand, if state-of-the-art simply means the models being employed today on real problems, then I think there's a lot more hope. The central problem with the models being studied by the ML research community (e.g., transformers and large language models), is the enchantment the ML community has with increasingly large parameter counts and increasingly sophisticated architectures. This trend makes reasoning about these models more and more difficult as it progresses, outpacing the progress of the verification community. What is needed in this context is time for computing resources and software to catch up. On the other hand, where the size of a model can be kept reasonable, and its use is sufficiently sensitive so as to warrant more effort on verification (e.g., medicine, cyber-physical systems), I think that it is possible to define specific properties that models must satisfy and to define the models themselves as well as their settings in a concrete enough way so as to be amenable to being formally reasoned about. I think a next step in this direction is in the intersection of formal reasoning and probability, as proving properties of statistical machine learning models can be prohibitively computationally expensive.

## Homework 2

the ACM Ethics Code

#### Reading thoughts:

The ACM Ethics Code doesn't seem to be a very serious set of ethical guidelines to me. Much of it is written and defined in a hand-wringing way that doesn't really take many strong positions. Excerpts like "unless there is a compelling ethical reason to do otherwise", "as much as possible", and "ensure that [] harm is ethically justified", really seem to undermine the strength of some of the code's reccommendations.

I will say though, I'm really glad they didn't leave out an important one: "Appropriate human-computer ergonomic standards should be used in the workplace".

#### Homework prompt:

Imagine that you are a professor in charge of a research group, working on proof automation that makes it easier to prove operating systems secure. DARPA offers you $10,000,000 in support of your research, with the caveat that you must demonstrate the success of your automation to build a verified operating system that makes a drone resilient to remote hijacking. Do you take the money? What ethical concerns factor into your decision? What points from the ACM Ethics Code do you find relevant?

#### Response:

I like this prompt because it is a sufficiently thick account to make an ethical argument I've been fencing with my advisor over for years. I believe that a person who contributes to the development of a technology that can forseeably be used for a specific set of ends, is responsible for those ends to a degree proportional to how much their efforts hastened those ends. Put simply, if your work makes something happen that wouldn't have otherwise happened, you are partially to blame. This is the tip of my spear when arguing that technology isn't neutral, and something I should expand on in another blog post sometime. 

The immediate relevancy to the question at hand is that my contribution to the safety of military drones, which are almost certainly going to be used to kill more than any other purpose (despite all those ads trying to convince of the contrary), plays a role in my partial culpability for those killings. Obviously there are many details that can be explored, including the positive outcomes of the work, but I'm not personally interested in moral calculus. I'd rather not contribute to killings where I can avoid it, as a rule. I could not take the money, even if it meant a rockier path down the same research road.

I don't think any of the ACM code applies to this question because it seems to have been written in a way that makes collaborating with power excusable in many ways. The point that seems to most directly relate to the question is 1.2, "Avoid harm". However, this excerpt totally removes the teeth in this situation: "When harm is an intentional part of the system, those responsible are obligated to ensure that the harm is ethically justified."

## Homework 3

Chapter 4 of QED at Large: A Survey of Engineering of Formally Verified Software by Talia Ringer, Karl Palmskog, Ilya Sergey, Milos Gligoric, and Zachary Tatlock.

#### Homework prompt:

Imagine that you are designing a new proof assistant, from the foundations up. What do you consider in your design of the foundations, and why?

#### Response:

I think I would design it from the ground up for use as an extension of common programming languages to support an alternative to testing to rule out entire classes of bugs at compile time. This is directly inspired by how Rust extended its type system to handle lifetimes, basically ruling out errors associated with pointer targets prematurely going out of scope. With a minimal amount of help from the programmer, static analysis can prove an awesome amount about a program. I've no doubt that with good proof assistance, that much more could be proven at compile time with minimal programmer effort and maximal impact.

With respect to the considerations required to support this usage, I'm really unsure of what choices would best support this. I'm hoping that's what I can learn in this course, because I'd really love to help build systems like this. I think they could work wonders in creating safe software for the real world.

#### Bonus prompt:

Alan Turing wrote the first proof of program correctness, and yet Hoare and Floyd are largely credited for the earliest verification work. Why do you think this is?

#### Response

Same reason why we don't credit Euler with everything in mathematics. If we named everything he touched on first after him, we'd have to start subtitling theorems. It's simply well understood that he's the ancestor of a huge amount of mathematics, like Alan Turing is the progenitor of most of computer science. (Side note: Alan Turing is one of my heroes)

## Homework 4

First class excercise with Agda. To kick things off, I'd like to re-iterate my hatred of Emacs and my wishes that there was another good medium for working with the likes of Lisp and now Agda. Setting everything up was pretty easy until I had to figure out how to do anything at all with Emacs. I'm a Vim gal, but I've always been pretty flexible with my editors. All I'll say is that everyone I know who uses Emacs is in wrist braces for carpal tunnel-type symptoms.

DISCUSSION QUESTION: What was it like constructing these proofs directly,
as terms in a programming language? What did you find challenging about
this experience, if anything? What did you find helpful, if anything? Did
you get stuck at any point, and if so, where and why? Where do you wish
you'd had more automation to help you out?

My pain points with the process were mainly being unsure of what the error messages actually mean about my program. I understand that the types aren't unifying like I want them to, but something that bridges the gap between my potential intentions and what the compiler expects would be really helpful. Rust does a fantastic job of this with its error messages, and this trial-and-error effort is redoubling my appreciation for it.

I got nerd sniped really hard by this assignment. I got stuck at the last hole because I didn't really understand how to use substitution as defined (mostly because the notion of a "property" isn't clear). I think the lack of references to Agda syntax and basics is a definite gap in this assignment. I know Haskell pretty well, but that only goes so far here.

The fact that we can prove so much so easily is really neat here. I'm used to proofs being numerical and requiring a lot of effort, so being able to write proofs with small, algebraic terms is fun.
