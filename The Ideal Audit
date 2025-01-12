### `*Protocol Name & Site - 30-10-2024_Swan & CodeHawks*`

### `*Number of Days  -  7*`

### `Number of SLOC - 1050`

### `*what it does ?  - A protocol`*

### ***Files in Scope:***

```solidity
**Src:**

core/ 
Swan.sol - 200 SLOC - Whole Main Logic stays here
SwanAsset.sol - 500 SLOC - User Asset logic and transfer

governance/
ParamGovernor.sol - 300 SLOC - Protocol governance system working & params
```

```solidity
***README.md:

## Goals : 3(1.5H) Session for this
- Recon Phase: To collect these goals you also have to glance over the contracts.
 
 
                                                                     (3-4 days)  
Step 1. Walk through these how this can done/ Walk-through CodePaths.(10-12 Sessions) 
Step 1:2. Add Tags for Question and Potential Bugs or edge-cases.
Step 1:3. Focus on getting context while putting thes tags in backlog to look into.
Step 2. Diagram through as you understand what contract file does what.
Step 2:1. Put through your more than 75% energy in to above mentioned things.
Step 3. Go through the audit tags give them a second pass
Step 3:2. Start Writing reports

## Notes :

##* Governance, Upgradeability, ERC-4626, Oracle, Signature ?**

 
**## Tokens:

## Actors:

## Areas to focus for bugs:

*## Findings  :***

```

 

### `Who can call what public functions :`

### `Owner:`

```solidity
src/
Swan.sol - addOwner() , removeOwner()
```

### `User:`

```solidity
src/core/
SwanAsset.sol - providelptoken() , withdrawToken()
```

### `Who can call what external functions :`

### `Owner:`

```solidity
Swan.sol - watchOwner() , startOwner()
```

### `User:`

```solidity
SwanAsset.sol - setTokenDepo() , withdrawTokenDepos()
```

```solidity
***## Approach this way:

- Start the audit with a *GROWTH MINDSET* 
   1. Ask what am I gonna learn in this (eg: factoryContracts, governance, ERC-4626)
   2. Instead of focusing on $$ focus on learning the system & growing your toolkit
   3. See how the above(2.) mentioned things are implemented 
   Note: If something is new like:ERC-4626 then go through the *notion.so/Audit-Brain Notes*
  
- Start taking a glance on files and understand their key functions 
   1. Go on smaller files first (like: ERC-20 or 721)
   2. See what each file does  
   3. Try reading bottom to top 
   4. Look out for Assemtric function (like: if mint() is there then burn() should also)

TIP: Challenge developer assumptions (sometimes in comments).  

- Look for these 3(AAA) in audits:
   1. first is Assets (look through how they get into the system and how they exit or how they flow over the contracts).
   2. Now, Actors ( go through what role can call what function and what is intended to call by whom)
   3. Last Check Actions (when and what can be done on what and whom can do it eg: calculations, state changes or adding and removal of borrowers)
   
  
- Try & Submit POCs in audit contest
   1. More likely to get accepted
   2. Treat these POCs as part of your resume you are building for doing privaet audits
       or working at firms in future***
   
   

```
