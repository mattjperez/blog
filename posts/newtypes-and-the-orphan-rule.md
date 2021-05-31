# Newtypes and the Orphan Rule

_31 May 2021 · #rust_

**Table of Contents**
- [Intro](#intro)
- [Conclusion](#conclusion)
- [Discuss](#discuss)
- [Notifications](#notifications)
- [Further Reading](#further-reading)



## Intro

Example Scenario (Intent, Implementation, Resulting Error)
- Print a struct from another library (ex. Duration from std::time)
- Make external struct printable using Debug trait (also external)
- Error codeblock

## Orphan Rule
-  Reasoning (Cohesion)
- Examples
    - If user impl TraitFromA on TypeFromA, maintainers can no longer implement it without risking break in user code
    - If user impl TraitFromA on MyType<TypeFromA>, maintainers can impl TraitFromA for TypeFromA without risking break
- Example of MyTrait on ExternalType
- Example of ExternalTrait on MyType 

Problem: I don't want to make a new implementation of ExternalType to satisfy Orphan Rule
Solution: Newtype wrapper around ExternalType

## Newtypes

### Accessing Inner Fields

### Making Inner Fields Public

#### Deref
- When and where to use it
- Itertools library that extends functionality of Iterator trait

### Generic Newtypes

## Extras

## Conclusion

## Discuss

Discuss this article on
- [Github](https://github.com/mattjperez/blog/discussions)
- [Twitter](https://twitter.com/pretzelhammer/status/1379561720176336902)


## Notifications

Get notified when the next article get published by
- [Following mattjperez on Twitter](https://twitter.com/mattjperez) or
- [Subscribing to this repo's release RSS feed](https://github.com/mattjperez/blog/releases.atom) or
- Watching this repo's releases (click `Watch` -> click `Custom` -> select `Releases` -> click `Apply`)


## Further Reading
//TODO Fill with previous articles

- [Newtypes and the Orphan Rule](./newtypes-and-the-orphan-rule.md)


