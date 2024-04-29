# Interaction Networks
Using the .entities and .tokens files generated by bookNLP, the algorithms leverage the dependency relations and POS tagging to build networks of interactions between characters. 

## Example:

>***We(nsubj)** haven’t had her round in such a long time, and if you were to go and see her, even if it ’s a bit awkward… but you know , it’s not as though she doesn’t understand the difficulty.*

The objective is to find the characters that are interacting. In this sentence ***We***/had/***her*** and ***you***/see/***her*** are interacting.

All algorithms start by extracting the end tokens of entities from the .entities file for a given sentence. We filter for PER tags to ensure that we're only iterating through characters.

<div align="center">
   <img width="304" alt="Entities Visualizer" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/54303357-2366-459e-ac77-2e4e9f9da937" >
    <br>
    <p><i>Example of BookNLP .entities file</i></p>
</div>

The algorithms then match the end tokens from the .entities file with the tokens in the .tokens file.

<div align="center">
  <img width="475" alt="Screenshot 2024-04-29 at 3 41 37 AM" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/6ea6d258-6d9a-4cca-b2f5-b0fde8c92f27">
    <br>
    <p><i>Example of BookNLP .tokens file</i></p>
</div>

The algorithms then find the entities that are subjects(nsubj) or objects(dobj, pobj). What's considered an object can change depending on the algorithm.

<div align="center">
  <img width="469" alt="Screenshot 2024-04-29 at 3 42 16 AM" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/02678ba6-bfa3-40dd-abc2-96fbc248b0e5">
    <br>
    <p><i>Example of BookNLP .entities file</i></p>
</div>



The hopping algorithms iterate through the objects and perform "hops". They go through each objects syntactic head ID's to find any subjects they connect to.
<div align="center">
  <img src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/0439dfdc-c8be-42b9-b950-e9f5af88565b" alt="Dependency Visualization">
    <br>
    <p><i>Dependency Visualization of the Sentence.</i></p>
</div>



After finding the connections, all algorithms output a dataframe in the following format:

<div align="center">
 <img width="400" alt="Screenshot 2024-04-29 at 4 17 51 AM" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/3722428d-1ba9-4138-8869-f71da3e5a5fd">
    <br>
    <p><i>Output Dataframe.</i></p>
</div>




## Basic Co-occurrence  Network:
***Precision:*** 0.21839 <br>    ***Recall:*** 0.97436 <br>   ***F1:*** 0.35681 

TP: 114 FP: 408 FN: 3

The function extracts and pairs all unique entities from a given sentence, ensuring each pair consists of entities with different coreference labels. This process is done by using combinations of entity tuples, each containing a word and its coreference label. Pairs with the same coreference label are discarded to focus only on relationships between distinct entities. Unlike the other network building methods, this one non-directional and has undirected edges (i.e., entity A co-occurring with entity B is equivalent to entity B co-occurring with entity A).


### Examples:

>***We(nsubj)** haven’t had her round in such a long time, and if you were to go and see her, even if it ’s a bit awkward… but you know , it’s not as though she doesn’t understand the difficulty.*

<div align="center">
   <img width="304" alt="Entities Visualizer" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/ace8963b-45b6-4c34-94c8-836681696c8e" >
    <br>
    <p><i>Finds all the entities in a given sentence and pairs them</i></p>
</div>

<div align="center">
   <img width="304" alt="Entities Visualizer" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/455c2aed-c6de-4480-b23a-7e3d31e86cac" >
    <br>
    <p><i>Output</i></p>
</div>




>***She**’d lost **David**.*

The model successfully captures the connection between **She** and **David**

<br>


>***I** met **a man driving a wagon** and asked him how far it was to the kingdom and what direction.*

The model successfully captures the connection between ***I*** and ***a man driving a wagon***

<br>

>*From the rim **I** could see my **father** and **Jane** on horseback , and **a number of the slaves** , including **Toby** , whose liberation once proposed by **OGF** had been denied for now by Plantation House .*

Since this method doesn't consider the relationship between the entities in the sentence aside from them being within the same sentence, this method connects all the entities with each other despite not all of them necessarily interacting with each other which results in a lot of false positives. 


<br>

>***An elderly philologist** told Glikman that only once before in his lifetime had he witnessed such a vast and insistent ovation : forty - four years previously , when **Tchaikovsky** had conducted the premiere of his Sixth Symphony .*

In this example, the connection between ***An elderly philologist*** and ***Tchaikovsky*** is successfully found. Both entities are labelled as nsubjs so all the other methods miss this connection. 


<br>
<br>


## Single Hop:
***Precision:*** 0.90244 <br>    ***Recall:*** 0.31624 <br>   ***F1:*** 0.46835

The singlehop function designed to extract relationships between entities based on their syntactic roles within a sentence, specifically focusing on connections between subjects(nsubjs) and direct objects (dobj) that share the same verb. The function first maps token IDs to their corresponding coreference labels, enabling identification of entities. It then filters entities that are subjects and objects based on these roles and checks if they share a common verb by comparing their syntactic head IDs. This function only performs one "hop" between syntactic head ID's and thus only finds direct connections between entities.


### Example:

> * ***Ryan(nsubj)*** led ***him(dobj)*** into the kitchen.*

<div align="center">
   <img width="304" alt="Entities Visualizer" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/fe0a0be8-b506-4bd7-a3ad-8a164ac7c7db" >
    <br>
    <p><i>Finds the entities from the .entities file</i></p>
</div>

<div align="center">
   <img width="304" alt="Entities Visualizer" src="https://github.com/samlierl1/Interaction-Networks/assets/74319676/bf24fac0-ea7c-4f14-8efd-ccf3bbe263f1" >
    <br>
    <p><i>Makes the connection between Ryan(nsubj) and him(dobj) since their syntactic head ID's lead to the same verb (single hop). </i></p>
</div>

<br>
<br>


## Basic Double Hop:
***Precision:*** 0.76623 <br>    ***Recall:*** 0.50427	 <br>   ***F1:*** 0.60824

The doublehop function is an extension of the singlehop function, designed to identify more complex relationships between entities by considering a second layer of connections beyond the immediate syntactic roles. Like singlehop, it identifies subjects (nsubj) and but this time also includes objects (dobj, pobj) based on their dependency relations and filters for entities listed in a coreference database. Unlike the singlehop function the function now considers pobjs as objects. After establishing a direct connection between subjects and objects sharing the same verb, doublehop looks further by exploring potential secondary relationships. Specifically, it checks if any prepositional object (pobj) directly connected to a verb has a subsequent link back to the main verb shared with the subject, thus forming a double hop. This approach enables the function to capture not just direct actions but also related actions or states that involve the subject. 

### Examples:


>*And on the bridge , after we met the dinosaurs , the Dark Oculator fled not because **she** recognized **me** — but because **she** recognized **you** .*

In this example, the connections between ***she(nsubj)*** and ***you***/***me(dobjs)*** are successfully made. In both cases, the syntactic head ID's of ***she(nsubj)*** and  ***you***/***me(dobjs)*** connect to **recognized** which is why the connections are made. 

<br>

>*What **I** ’ve learned from **Papaw Phil** about never giving up is that there is nothing wrong with starting small if you just keep going .*

In this example, the connection between ***I(nsubj)*** and ***Papaw Phil(pobj)*** is successfully made. The syntactic ID of ***I*** leads to ***learned***.
***Phil*** hops to *from* which then hops to **learned**, connecting the two entities:

***Phil***->from->learned->***I***

<br>
<br>











# Added Double Hop Conditions:

![joe123](https://github.com/samlierl1/Interaction-Networks/assets/74319676/d77386d0-7c1d-40e9-be25-b42982427632)

## 1. Inclusion of additional dependency relations: The function now considers entities with dependency relations conj, dative, and attr in addition to pobjs and dobjs as objects. 

***Precision:*** 0.76190		<br>    ***Recall:*** 0.54701 <br>   ***F1:*** 0.63681592039801

### Conjunctions(conj) as objects:

> *From the rim ***I*** could see my father and ***Jane*** on horseback , and a number of the slaves , including Toby , whose liberation once proposed by OGF had been denied for now by Plantation House .*

With this condition in place the connection between ***I*** and ***Jane*** is now made. When an entity is labelled as a conj it is typically because the entity is associated with another noun.  "Jane" is labelled as conj in relation to "my father" indicating that they are participating in the same activity or are in the same state (both are seen on horseback).

### Datives(dative) as objects:
>*She looks at me as **I** hand **her** some tiny earphones , and she puts them into her ears before I press a button .*

With this condition in place the connection between ***I(nsubj)*** and ***her(dative)*** is now made. Datives often signify an important relational dynamic where one entity is benefitting from or being affected by the actions of another. The dative "her" represents the recipient of the action. In this case, "her" is the indirect object receiving the earphones that "I" hands over.

### Attributives(attr) as objects:

>***I** thought it was **you** what are you doing on Victor Emilio Estrada ?*

With this condition in place the connection between ***I(nsubj)*** and ***you(attr)*** is now made. In dependency parsing, the "attributive" (attr) label is used to connect a noun (or pronoun) that serves as a subject complement to its subject. Here, "you" completes the predicate and describes or identifies "it," linking back to what "I" thought. 

However, this does also raise errors.

>***She** was the seventh **witness** presented by the prosecution in two days but as far as I was concerned she was the only witness .*   

With this condition in place the connection is incorrectly made between ***She(nsubj)*** and ***witness(attr)*** is now made. In this case ***witness*** is considered an attr because it acts as the complement to ***She***. However, because witness is considered a different entitiy to She a connection is incorrectly made between the two.

<br>
<br>


## 2. Handling of possessive (poss) entities

***Precision:*** 0.76744		<br>    ***Recall:*** 0.56410 <br>   ***F1:*** 0.65024

The function integrates special handling for entities marked with the poss dependency, which indicates possession. If a possessive entity's head ID has a dependency of nsubj, it is added to the nsubjs. If the head ID's dependency is dobj or pobj, the entity is added to obj_entities. This ensures that possessive references are correctly aligned with the entities they modify or relate to.

>'Her body spasms into mine , and we tremble together as ***she(nsubj)** clutches ***my(poss)*** ***neck(dobj)*** in a chokehold with her knees .'

Adding poss handlings ensures that possessive modifiers like "my" are correctly associated with their respective nouns, in this case, "neck," allowing for the connection to be made between she and my.
 
<br>
<br>
## 3. Cleaning up False Positives: Removing *anyone, people, nobody, anybody* as entities and removing overlapping entities:

***Precision:*** 0.825		<br>    ***Recall:*** 0.56410 <br>   ***F1:*** 0.670051

Excludes words like "anyone," "people," "nobody," and "anybody" from being considered as entities. This addresses a specific issue related to how certain non-specific nouns are handled in entity recognition processes. In many natural language processing systems, including BookNLP, words that refer to general groups of individuals or unspecified persons can sometimes be mistakenly tagged as entities.

>***She(nsubj)** walked with the busy , measured tread heavy **people(pobj)** sometimes had , and , like a train , sighed whenever she started or stopped .*

In the previous version of Double Hop, a connection between ***She*** and ***people*** would be made. The word "people" here does not refer to a specific individual but rather to a general characteristic of a group of individuals ("heavy people"). In the context of analyzing character interactions, forming a connection between "She" and "people" would be inaccurate because it doesn't represent a direct interaction between two specific characters. 


 
<br>
<br>
## 4. Allowing an extra hop if the first word in the hopping process is labelled as a 'prep' (preposition) or 'xcomp' (open clausal complement).
***Precision:*** 0.79412		<br>    ***Recall:*** 0.69231 <br>   ***F1:*** 0.73973

### Example of 'prep' (preposition) in the hopping process

>*Needing to hear what this **boy** has to say about **her**.*

With this condition in place, ***boy*** and ***her*** get successfully connected. 

***her(pobj)***->***about(prep)***->say->has->***boy(nsubj)***

Prepositions are typically words like "in," "on," "at" or "about". They modify the verb that they're connected to and thus an extra hop being allowed if the first word in the hopping order is a preposition allows a lot of new connections to be made. Not counting words labelled as "prep" in the hop count prevents the inclusion of intermediary words that might not contribute significantly to the relationship between entities and impact potential connections.


### Example of 'xcomp' (preposition) in the hopping process

>*The only sadness to the trip was that ***Johnny Lucinge*** was not well enough to accompany ***her***.* 

***her(dobj)***->***accompany(xcomp)***->enough->was->***Johnny Lucinge(nsubj)***

The purpose of an 'xcomp' is to add information that completes the meaning of the main verb in the sentence. Adding an extra hop when an xcomp is present increases the likelihood of finding the verb that connects both entities. 









<br>
<br>




<br>
<br>

<br>
<br>

<br>
<br>

<br>
<br>

<br>
<br>

<br>
<br>

<br>
<br>




