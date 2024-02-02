# Development Documentation

## Table of Contents

- [Pre-Development Considerations](#pre-development-considerations)
    - [Introduction](#introduction)
    - [General Information](#general-information)
        - [Signature Rules](#signature-rules)
    - [API Documentation](#api-documentation)
        - [Player Login](#player-login)
        - [Transfer In/Out](#transfer-inout)
        - [Game List](#game-list)
        - [Enter Game](#enter-game)
        - [Transfer Order](#transfer-order)
        - [Game Order](#game-order)
        - [Player Information](#player-information)
 - [Games](#games)
  - [Game Introduction](#game-introduction)
- [Changelog](#changelog)
- [Contact Us](#contact-us)


# Pre-Development Considerations
## General Information


### Signature Rules
This method is used for direct MD5 signature.

#### Signature Algorithm

##### Step 1
Let all the data sent or received be a set M. Sort the non-empty parameter values in the set M in ascending order of ASCII code of parameter names (lexicographical order), and concatenate them into a string stringA i
