---
title: Datalog
permalink: /rules
sidebar: docs_sidebar
folder: docs
---

Rules are Horn clauses that are disjunctions of literals with at most one positive literal
that can be written in implication form as,

**u** ⇐  **p** ∧ **q** ∧ ... ∧ **t**

A rule can be read as: predicate **u** holds if the conditions
**p** ∧ **q** ∧ ... ∧ **t** hold.
We refer to predicate **u** as the head (aka. consequent), and
the condition **p** ∧ **q** ∧ ... ∧ **t**  as body (aka. antecedent),
repectively.

A rule is a definite clause that has a predicate as a head, and one or more 
literals in the body.
The literals are predicates (i.e. logical functions with arguments that
are either constants or variables), negated predicates, or constraints.
All variables of a rule must be *grounded*, i.e., a variable must occur
at least once as an argument of a positive predicate in the body of
a rule.  Soufflé permits arithmetic and string functors assuming
that their arguments are grounded.

 ```A``` and ```B``` with two number attributes.
```prolog
.decl A, B(x:number, y:number)  // declaration of relation B
.input A                     // read A 
B(x,y) :- A(x,y).            // rules of relation B
B(x,z) :- A(x,y), B(y,z).
.output B
```
Relation ```B``` has two rules: ```B(x,y) :- A(x,y).``` and ```B(x,y) :- A(x,y), B(y,z).``` The first rule says, there is a tuple in `B` if this tuple shows up in `A`. The second rule says there is a tuple `(x,z)` in `B`, if there is a tuple in `(x,y)` in `A` and a tuple `(y,z)` in `B`.

Rules may have qualifiers, which are used to change the execution behavior / semantics. Qualifiers are used to set a query plan for a rule. For example, the qualifier `.strict` forces the query planner to use the lexicographical order of the rule (how it is written). The qualifer `.plan` let's the programmer chose a query plan for a rule. There are other qualifiers that are used to override rules of the a component (cf. [component model](components)). 

Rules can have multiple heads:
```
A(x,y), C(x,y) :- B(x,y). 
```
which is syntactic sugar for
```
A(x,y) :- B(x,y). 
C(x,y) :- B(x,y). 
```

# Negation in Rules

A rules of the form
```
CanRenovate(person, building) :- Owner(person, building), !Heritage(building).
```
expresses the rule that an owner can renovate a building with the condition that the building is not classified as heritage. Thus the literal "Heritage(building)" is negated (via "!") in the body of the rule. Not all negations are semantically permissible. For example,
```
A(x) :- ! B(x).
B(x) :- ! A(x).
```
is a circular definition. One cannot determine if anything belongs to the relation "A" without determining if it belongs to relation "B". But to determine if it is a "B" one needs to determine if the item belongs to "A". Such circular definitions are forbidden. Technically, rules involving negation must be stratifiable.

Negated literals do not bind variables. For example,
```
A(x,y) :- R(x), !S(y).
```
is not valid as the set of values that "y" can take is not clear. This can be rewritten as,
```
A(x,y) :- R(x), Scope(y), !S(y).
```
where the relation "Scope" defines the set of values that "y" can take.

# Disjunction

A rule of the form
```
LivesAt(person, building) :-
    Owner(owner, building),
    ( person=owner ; Housemate(owner, person) ).
```
expresses the rule that a person lives in a building if they are the owner, or a housemate of the owner. Thus the conditions `person=owner` and `Housemate(owner, person)` are joined by `;` to indicate that either must hold.

{% include links.html %}