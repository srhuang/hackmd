---
title: An Introduction to Formal Language and Automata
tags: Computer Science
description: 2019.09.11
---
An Introduction to Formal Language and Automata
===
###### *2019.09.11 Copyright © 2019 by srhuang*

## Chapter 1：Introduction to The Theory of Computation
* automata theory, formal language and grammars, computability, and complexity constitutes the theoretical foundation of computer science.
*  Why study theory?
    * theory provides concepts and principles that help us understand the general nature of the discipline.
    * the ideas we will discuss have some immediate and important applications.e.g. digital design, programming languages, and compilers.
    * the subject matter is intellectually stimulating and fun.
* Automata : a construct that possesses(具有) all the indispensable(不可或缺) features of a digital computer. It accepts input, produces output, may have some temporary storage, and can make decisions in transforming the input to output.
* Formal Language : an abstraction of the general characteristics of programming languages.
* Mathematical preliminaries and notation sets : 
    * 「set」 operations : union, intersection, difference, complementation.
    * DeMorgan’s laws：https://en.wikipedia.org/wiki/De_Morgan%27s_laws
    * subset, proper subset, disjoint sets, powerset.
* Functions and Relations : 
    * Functions is a rule that assigns to elements of one set(domain) a unique element of another set(range).
    * Relations are more general than functions.
* Graph：
    * A graph is a construct consisting of two finite sets, the set V of vertices and the set E of edges. Each edge is a pair of vertices from V.
    * A path is simple if no vertex is repeated.
    * An edge from a vertex to itself is called a loop.
* Tree：
    * Trees are a particular type of graph：directed graph that has no cycles.
* Proof Techniques
    * proof by induction. (歸納法)
    * proof by contradiction. (反證法)
        * 證明根號 2 是無理數。
* Three Basic Concepts
    * Languages：alphabet, strings, concatenation, reverse, length, substring, prefix, suffix, star-closure(所有組合，包含 empty string), positive closure (不包含 empty string)。
    * Language 是個集合 (set)，背後的規則 (rules) 就是 Grammars。
    * Grammars：tell us whether a particular sentence is well-formed or not.
        * A grammar G is defined as a quadruple : variables, terminal symbols, start variable, productions. 
        * The production rules are the heart of a grammar.
        * derivation, sentential forms.
    * Automata：is an abstract model of a digital computer.
        * every automaton includes some essential features：input file, temporary storage, control unit, output.
        * deterministic automata：each move is uniquely determined by the current configuration.
        * nondeterministic automata：may have several possible moves.
        * accepter : output response is limited to a simple “yes” or “no”.
        * transducer：producing strings of symbols as output.

## Chapter 2：Finite Automata
*  Deterministic Finite Accepters (DFA)：is defined by internal states, input alphabet, transition function, initial state, final states.
    * each move consumes one input symbol.
    * the string is accepted if the automaton is in one of its final states.
    * dfa M , L(M)：可以通過 DFA (named M) 的 string 集合。
    * To visualize and represent finite automata, we use transition graphs.
* Regular Languages：
    * A language L is called regular if and only if there exists some DFA M such that, L=L(M).
* Nondeterministic Finite Accepters (NFA)：
    * transition function range is a set of possible states.
    * can make a transition without consuming an input symbol.
    * there is no transition defined for the specific situation.
* Why Nondeterminism?
    * NFA machines can serve as models of search-and-backtrack algorithms.
    * NFA is sometimes helpful in solving problems easily.
    * NFA is an effective mechanism for describing some complicated languages concisely.
* Equivalence of Deterministic and Nondeterministic Finite Accepters
    * The classes of DFA and NFA are equally powerfully.
    * We can actually give a way of converting any NFA into an equivalent DFA.
    * Every language accepted by an NFA is regular.
* Reduction of the Number of states in Finite Automata
    * Any DFA defines a unique language. For a given language, there are many DFA’s that accept it.
    * procedure : reduce
        1. Generate the equivalence classes.
        2. For each set of such indistinguishable states, create a state.
        3. For each transition rule, find the sets to which belong.
        4. Adjust initial state.
        5. Adjust finite state.
* Todo：Write DFA in C.

## Chapter 3：Regular Languages and Regular Grammars
* Regular Expressions
    * One way of describing regular language is via the notation of regular expression.
    * L(r+q) = L(r\) union L(q)
    * L(r ．q) = L(r\)L(q), the symbol for concatenation may be omitted.
    * L(r*) = (L(r\))*
* Two regular expressions are equivalent if they denote the same language.
* Connection between Regular Expressions and Regular Languages
    * For every regular language there is a regular expression. (GTG to REX)
    * For every regular expression there is a regular language. (construct NFA for REX)
* Generalized transition graphs (GTG)：is a transition graph whose edges are labeled with regular expressions.
* NFA to REX procedure
    1. distinct other states from its initial state.
    2. convert NFA into a complete GTG.
    3. If GTG has only two states, generate associated regular expression.
    4. If GTG has three states, remove third state, and introduce new edges.
    5. If GTG has four or more states, pick a state to apply rule 4.
    6. repeat step 3 to 5 until REX is obtained.
* regular expression for pattern matching.
* How to describe regular languages：NFA, REX, grammars.
* Right-Linear Grammars / Left-Linear Grammars：A->xB / A->Bx.
* A regular grammar is one that is either right-linear or left-linear.
* Let G be a right-linear grammar. Then L(G) is a regular language.
    * Using right-linear grammar to construct NFA。
* If L is a regular language, then there exists a right-linear grammar G such that L=L(G).
* A language L is regular if and only if there exists a regular grammar G such that L=L(G).

## Chapter 4：Properties of Regular Languages
* Closure Properties of Regular Languages
    * the family of regular languages is closed under union, intersection, concatenation, complementation, and star-closure, difference, reversal.
* Introduce homomorphism ：substitution in which a single letter is replaced with a string.
    * If L is a regular language, then its homomorphism image is also regular.
* Right quotient : L1/L2 = {x : xy belongs to L1 for some y belongs to L2}. removal of suffix belonging to L2.
    * If L1 and L2 are regular languages, then L1/L2 is also regular.
* Elementary Questions about Regular Languages
    * membership algorithm : Given a language L and a string w, can we determine whether or not w is an element of L?
    * regular language is given in a standard representation if and only if it is described by a finite automaton, a regular expression, or a regular grammar.
    * given standard representation of two regular languages L1 and L2, there exists an algorithm to determine whether or not L1=L2.
* Identifying Nonregular Languages
    * Pigeonhole Principle (鴿籠原理) : If we put n object into m boxes and if n>m, then at least one box must have more than one item in it.
    * Pumping Lemma : Let L be an infinite regular language. Then there exists some positive integer m such that any w belongs to L with |w| >= m can be decomposed as w=xyz such that w=xy*z is also in L.
        * proof hint : in a transition graph with n vertices, any walk of length m>n, must repeat some vertex, that is, contain a cycle. 
    * pumping lemma only for infinite languages.
    * Mistake for pumping lemma
        * using pumping lemma to show that a language is regular.
        * to start with a string not in L.
        * the decomposition is what the pumping lemma tell us, the assumption on y is not permitted.

## Chapter 5：Context-Free Languages
* Context-Free Grammars : all productions in P have the form A -> x.
    * Context-Free Grammars retaining the restriction on the left side, but permitting anything on the right.
    * A language L is said to be context-free if and only if there is a context-free grammar G such that L=L(G).
    * Every regular grammar is context-free. Every regular language is also context-free.
    * Linear Grammar : there is at most one nonterminal symbol at right side.
    * Leftmost and rightmost Derivations.
    * Derivation Tree (Parse tree) / partial derivation tree.
    * Sentential form of the derivation : contain variables and terminals.
    * Every partial derivation tree represents some sentential form.
* Parsing and Ambiguity
    * Parsing : finding a sequence of productions by which a w belongs to L(G) is derived.
        * exhaustive search parsing (brute force parsing) is a form of top-down parsing.
        * exhaustive search parsing has nontermination problem : 
            * restrict the form that the Grammar can have : NOT have A-> empty ; A->B.
        * the work (sentential forms) may grow exponentially with length of the string.
        * Simple Grammar (s-grammar) : all its productions are of the form A->ax, and any pair (A,a) occurs at most once in P. [a belongs to terminal ; x belongs to vertices]
    * Ambiguity : there exists some string that has at least two distinct derivation trees.
        * One way to resolve is to associate precedence rules.
        * inherently ambiguous : every grammar that generates L is ambiguous.
    * Using Formal Language : 
        * definition of programming languages.
        * interpreters.
        * compilers.
    * Backus-Naur Form (BNF) :

## Chapter 6：Simplification of Context-Free Grammars and Normal Forms
* Methods for Transforming Grammars : lambda-free languages
    * A useful Substitution Rule.
    * Removing Useless Productions : we can draw a dependency graph for the variables.
    * Removing lambda-Productions
    * Removing Unit-Productions
* grammars for context-free languages can be made free of useless productions, lambda-productions, and unit-productions : remove lambda —> remove unit —> remove useless.
* Two Important Normal Forms
    * Chomsky Normal Form : A->BC or A->a.
    * Greibach Normal Form : A->ax.
* A membership algorithm for context-free grammars : CYK algorithm for Chomsky Normal Form.

## Chapter 7：Pushdown Automata
* Nondeterministic Pushdown Automata (Accepter)
    * Definition : 
        * internal states (control unit)
        * input alphabet
        * stack alphabet
        * transition function
        * initial state
        * stack start symbol
        * final state
    * Transition graph
    * Instantaneous Description
    * The Language accepted by a Pushdown Automata
* Pushdown Automata and Context-Free Languages
    * Every context-free language there is an nada that accepts it, and that the language accepted by any nada is context-free.
    * For any context-free language L, there exists an NPDA M such that L=L(M).
        * Using Greibach Normal Form for context-free grammar. (Not necessary)
    * If L=L(M) for some NPDA M, then L is a context-free language.
* Deterministic Pushdown Automata and Deterministic Context-Free Languages
    * A language L is said to be a deterministic context-free language if and only if there exists a DPDA M such that L=L(M).
    * deterministic and nondeterministic pushdown automata are not equivalent.
    * There are context-free languages that are not deterministic.
* Grammars for Deterministic Context-Free Languages
    * s-grammars : Every s-grammar is an LL grammar.
    * LL grammars : more general than s-grammar, subsets of deterministic context-free grammars.
    * LR grammars : for every deterministic context-free language.

## Chapter 8：Properties of Context-Free Languages
* A pumping lemma for context-free languages.
    * if w belongs to L, w=uvxyz, uv(^i)xy(^i)z belongs to L.
    * Using derivation tree.
* A pumping lemma for Linear Languages
    * Linear Grammar : there is at most one nonterminal symbol at right side.
* Linear languages is a proper subset of the context-free languages.
    * There is an example belongs to context-free language but NOT in linear language.
* Closure Properties for Context-Free Languages
    * The family of context-free languages is closed under union, concatenation, and star-closure.
    * The family of context-free languages is NOT closed under intersection and complementation.
    * Regular intersection : L1 be a context-free language and L2 be a regular language. Intersection of L1 and L2 is context-free.
* Decision Algorithm for Context-Free Languages
    * Given a context-free grammar G, there exists an algorithm for deciding whether or not L(G) is empty / infinite.
    * there is no such algorithm to determine whether two context-free grammars generate the same language. (regular language in theorem 4.7)

## Chapter 9：Turing Machine
* The Standard Turing Machine
    * Definition : associated with the tape is a read-write head that can travel right or left.
        * internal states
        * input alphabet
        * tape alphabet
        * transition function
        * blank / initial state / final states.
        * input alphabet is a subset of the tape alphabet.
        * halt state : which transition function is not defined.
        * no transitions are defined for any final state.
    * Transition Graph
    * main features : 
        * tape is unbounded in both directions.
        * deterministic.
        * no special input file / output device.
    * Instantaneous description
* Turing Machines as Language Accepters
    * The Turing machine enters a final state and halts, then w is considered to be accepted.
    * Turing machine can recognize some languages that are not context-free.
    * Turing machines are more powerful than pushdown automata.
* Turing Machines as Transducers
    * Turing-computable
    * addition, arithmetic comparison, copy strings, these can be done easily on a Turing Machine.
* Combining Turing Machines for complicated Tasks
    * Using block diagram
    * Using pseudocode
* Church Turing Thesis
    * 任何在算法上可計算的問題同樣可由圖靈機計算。

## Chapter 10：Other Models of Turing Machines
* Standard Turing Machine is equivalent to other more complicated models.
* nondeterministic Turing Machines are no more powerful than deterministic ones.
* Equivalence of Classes of Automata : Two automata are equivalent if they accept the same language.
* For demonstrating the equivalence, we often use the technique of simulation.
* Minor Variations on the Turing Machine Theme
    * Turing Machines with a Stay-Option.
    * Turing Machine Definition : each tape symbol can be composite of characters rather than just a single one. (tracks)
    * Turing Machines with semi-Infinite Tape : unbounded only in one direction.
    * The Off-Line Turing Machine : put the input file.
* Turing Machines with More Complex Storage
    * Multitape Turing Machines : Turing machine with several tapes.
    * Multidimensional Turing Machines : e.g. two-dimensional tape.
* Nondeterministic Turing Machines
    * nondeterministic behavior can be handled deterministically.
    * nondeterministic automata usually viewed as a accepters.
    * keeping all possible instantaneous descriptions of the nondeterministic machine on its tape.
* A Universal Turing Machine : reprogrammable Turing machine
    * Description of M ; tape contents of M ; Internal state of M.
    * the set is countable if we can produce a method by which its elements can be written in some sequence (enumeration procedure).
    * enumeration procedure cannot be called an algorithm, since it will not terminate when S is infinite.
    * The set of all Turing Machines, although infinite, is countable.
* Linear Bounded Automata
    * How much of the tape can be used is a function of the input.
    * A linear bounded automata is a nondeterministic turing machine.
    * deterministic IBA is not known whether they are equivalent to the nondeterministic version.
    * IBA are more powerful than pushdown automata (context-free languages).

## Chapter 11：A Hierarchy of Formal Languages and Automata
* A language L is said to be recursively enumerable if there exists a Turing machine that accepts it.
    * There is an enumeration procedure for every recursively enumerable language.
    * all recursively enumerable languages is countable.
    * Let S be an infinite countable set. Then its powerset 2^S is not countable.
    * There exist languages that are not recursively enumerable. (Using powerset)
    * There exists a recursively enumerable language whose complement is not recursively enumerable.
* A language is recursive in and only if there exists a membership algorithm for it.
    * If a language is recursive, then there exists an enumeration procedure.
* diagonalization : manipulation of the diagonal element of a table.
* If a language L and its complement are both recursively enumerable, then both languages are recursive.
* the family of recursive language is a proper subset of the family of recursively enumerable languages.
* A grammar G is called unrestricted if all the productions are of the form u->v.
    * Any number of variables and terminals can be on the left or righting any order.
* Any language generated by an unrestricted grammar is recursively enumerable.
* We are given a Turing Machine M and want to produce a grammar G such that L(G) = L(M).
    * V(ab), V(aib) : the first is saved input symbol, the second is used in the steps (tape), i is the states.
* For every recursively enumeration language L, there exists an unrestricted grammar G, such that L=L(G).
* Context-Sensitive Grammars and Languages
    * context-free language is a proper subset of the context-sensitive language.
    * For every context-sensitive language L (not including empty string), there exists some linear bounded automata M such that L=L(M).
    * If a language L is accepted by some linear bounded automaton M, then there exists a context-sensitive grammar that generates L.
* Relation Between Recursive and Content-Sensitive Languages
    * Every context-sensitive language L is recursive.
    * There exists a recursive language that is not context-sensitive.
* The Chomsky Hierarchy
    * Type 0 : recursively enumerable languages (unrestricted grammars).
    * Type 1 : context-sensitive language.
    * Type 2 : context-free language.
    * Type 3 : regular language.
* deterministic context-free language / recursive languages.
* linear / deterministic context-free.
* it is not known whether linear bounded automata is a proper subset of context-sensitive language.

## Chapter 12：Limits of Algorithmic Computation
* a function f is said to be computable if there exists a Turing Machine that computes the value of f.
* the result of a computation is simple “yes” or “no”, in this case, a problem being decidable or undecidable.
* Turing Machine halting problem : no such algorithm, undecidable.
* If halting problem were decidable, then every recursively enumerable language would be recursive.
    * halting problem and membership problem for recursively enumerable languages are nearly identical.
* A is reduced to a problem B if the decidability of A follows from the decidability of B.
    * The state-entry problem.
    * The blank-tape halting problem.
    * the function f(n) whose value is the maximum number of moves that can be made by any n-state Turing Machine that halts when started with a blank tape. This function is not computable.
* Undecidable Problems for Recursively Enumerable Languages
    * the problem of determining whether or not L(G)=empty is undecidable.
    * M be any Turing Machine, then the problem of whether or not L(M) is finite is undecidable.
    * Rice’s Theorem : any nontrivial property of a recursively enumerable language is undecidable.
* The Post Correspondence Problem
    * Emil Leon Post 1946.
    * https://www.youtube.com/watch?v=VZNN1OGoqr8
    * Using Dominos concept.
* The modified Post Correspondence Problem
    * the first elements of the sequences A and B play a special role.
    * The modified Post Correspondence problem is undecidable.
    * The Post Correspondence problem is undecidable.
* Undecidable Problems for Context-Free Languages
    * There exists no algorithm for deciding whether any given context-free grammar is ambiguous.
    * There exists no algorithm for deciding whether or not L(G1) intersection L(G2) = empty, for arbitrary context-free grammars G1 and G2.
* A Question of efficiency
    * complexity

## Chapter 13：Other Models of Computation
* Turing’s work was published in 1936.
* Recursive Functions
    * Primitive Recursive Functions
        * Composition
        * Primitive recursion
    * A function is called primitive recursive if and only if it can be constructed from the basic functions by successive composition and primitive recursion.
    * all primitive recursive functions is countable.
    * F denote the set of all functions. Then there is some function in F that is not primitive recursive.
    * C denote the set of all total computable functions. Then there is one function in C that is not primitive recursive.
    * Ackermann’s Function : total , computable function, is not primitive recursive.
        * For primitive recursive functions, one such property is the growth rate.
    * μ-recursive function : μ-operator and the operations of composition and primitive recursion.
* Post Systems
    * https://en.wikipedia.org/wiki/Post_canonical_system
    * each variable on the right must appear on the left.
    * A language is recursively enumerable if and only if there exists some Post system that generates it.
* Rewriting Systems
    * matrix grammars
        * the set of productions consists of subsets, each of which is an ordered sequence.
        * 一次有多個 productions.
    * phrase-structure grammars (constituency grammars) : 
        * grammars in the Chomsky hierarchy.
        * as opposed to the dependency relation of dependency grammars.
    * Markov Algorithm
        * Starting with a terminal string, productions are applied until the empty string is produced.
        * A language is recursively enumerable if and only if there exists a Markov algorithm for it.
    * L-Systems
        * parallel rewriting systems.
        * each step of a derivation, every symbol has to be rewritten.

## Chapter 14：An Overview of Computational Complexity
* Efficiency of Computation
    * using Turing Machine as the computer model.
    * how the computational requirements grow with the size of the problem.
    * Here we are interested only in the worst case.
* Turing Machine Models and Complexity
    * Satisfiability problem (SAT) : Given a satisfiable expression e in CNF, find an assignment of values that will make the value of e true.
    * Conjunctive Normal Form (CNF) : boolean expression.
    * two-tape machine can be simulated by a standard Turing Machine in O(n^2) steps.
    * nondeterministic Turing machine can carry out a computation in n steps. Then a standard Turing machine can carry out the same computation in O(k^(an) ) steps, where k is the maximum branching factor for the nondeterministic.
* Language Families and Complexity Classes
    * DTIME(T(n)) : if there exists a deterministic multitude Turing machine that decides L in time O(T(n)).
    * NTIME(T(n)) : if there exists a nondeterministic multitude Turing machine that decides L in time O(T(n)).
    * for each T(n), NTIME(T(n)) including DTIME(T(n)).
    * DTIME(n^(k+1)) including DTIME(n^k).
        * there are languages in DTIME(n^3) that are not in DTIME(n^2).
    * As T(n) increase, more and more of the families L(REG), L(CF), L(CS) are covered.
* The Complexity Classes P and NP
    * P = union (DTIME(n^i)), includes all languages that are accepted by some deterministic Turing machine in polynomial time.
    * NP = union (NTIME(n^i)), nondeterministic. polynomial time 驗證 input.
    * https://bluelove1968.pixnet.net/blog/post/222283186-%E8%AB%96p,np,np-hard,np-complete%E5%95%8F%E9%A1%8C
* Some NP Problems
    * The SAT problem
    * The Hamiltonian Path Problem
    * The Clique Problem
* Polynomial-Time Reduction
    * The SAT problem is polynomial-time reducible to 3SAT.
    * The 3SAT problem is polynomial-time reducible to CLIQUE.
* NP-Completeness and an Open Question
    * NP-compete language L : every language in NP is polynomial-time reducible to L.
    * If we can find one deterministic polynomial-time algorithm for any NP-complete language, then P=NP.
    * Cook’s theorem : The SAT problem is NP-complete.
    * SAT, 3SAT, CLIQUE, and HAMPATH are NP-complete.