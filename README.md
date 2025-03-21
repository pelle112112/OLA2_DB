# OLA2_DB

### Task 1

Herunder finder man vores Entiteter og ER-Diagram

![entities](ER-model/Entities.png)
![ER-Diagram](ER-model/ER-Diagram.png)

Ved udarbejdelsen af vores ER-Diagram overvejede vi om vi skulle have vores membership som ENUM, og på den måde ikke have en ekstra entitet, men vi ville ikke kunne placere price attributen i member, da det ikke ville overholde 3. Normaliseringsform. Udover dette ville besluttede vi at det ud fra et spørgsmål om vedligeholdelse ville give bedre mening at at gøre membership til en entitet, da det vil være lettere for en administrativ medarbejder at tilføje eller fjerne forskellige typer medlemskaber på denne måde, i modsætning til hvis det var et enum, hvorved en teknisk medarbejder ville være nødt til at ændre i selve modellen, hvis ændringer skal implementeres.

Vi overvejede også at lave vores ClassType (Under TrainingClass) til en ENUM, og besluttede af lignende hensyn til membership at oprette classType i stedet, for at håndtere fremtidige ændringer.

Udfra opgavebeskrivelsen mener vi at Discount skulle være en attribut hos den enkelte member, da der i opgavebeskrivelsen står "Medlemsrabatter: Kan gives i særlige tilfælde", hvilket hentyder til personlige rabatter, som ikke tilbydes i hele centeret. Dette kunne forekomme ved en aflyst træning eller andet.

Vi har valgt at lave en PaymentType i vores Member entitet, som indeholder fx Månedsvis abonnement eller klippekort.
Dette overvejede vi at separere i sin egen entitet kaldet "Payment", som ville indeholde paymentType, Discount og Price. 

Ud fra domænebeskrivelsen opstod der tvivl om det var betalingsdetaljer, eller faktiske betalinger, f.eks. en faktura der var tale om. Vores model er altså lavet med henblik på at holde betalingsdetaljerne, hvorefter det så var tanken at man kunne bruge en stored procedure eller function til at udregne de faktiske opkrævninger, som efterfølgende ville blive gemt i en anden tabel eller database. 

Skulle det derimod være hensigten med domænet at det var opkrævningerne, der var tale om. Ville modellen have set anderledes ud, og følge overvejelsen fra tidligere, hvor en payment entitet var oprettet, til at holde data, som price, discount, paymentType, have en relation til et medlemsskab og en due date / payment date. 

##### Multiplicitet:

Det fremgår ikke helt tydeligt af den håndtegnede model, så multipliciteten specificeres her.

member 0..n --has a-- 1..1 membership
Et member har altid en type medlemsskab, og hver type medlemsskab kan have ingen til n members.

member 1..1 --makes a-- 0..n booking 
En booking er altid lavet af et bestemt member, og det member kan lave så mange bookings de har lyst til.

booking 0..n --for-- 1..1 trainingClass
En booking er altid til en specifik trainingClass - en trainingClass kan have mange bookinger tilknyttet.

trainingClass 0..n --is a-- 1..1 classType
En trainingClass er altid af en bestemt type. Der kunne argumenteres for at en class burde have lov at have flere types, i tilfælde at types i højere grad skal bruges som tags til filtrering eller søgning, men i denne case hvor det fremgår uvist om dette er relevant for en business case, er typen begrænset til 1. En type hører dog til mange forskellige trainingClasses.

trainingClass 0..n --has an-- 1..1 instructor
Der er altid 1 instructor til en traningClass, men den instructor kan godt lede flere forskellige classes.


### Task 2

##### 1. Normalform

Første normalform er overholdt allerede ved udarbejdedelsen af entiter og ER-Diagram, da alle vores værdier er atomare.

##### 2. Normalform

Også 2. normalform var overholdt ved udviklingen af entiteter og ER-Diagram, men skulle der kigges på en måde at bryde 2. Normalform kunne man eksempelvis rykke startTime (fra TrainingClass) til Booking entiteten.

Dette ville give nogle udfordringer fordi starttiden vil gentages for alle bookinger tilhørende den samme trainingclass, hvilket ville gøre det besværligt at ændre start tidspunktet, eftersom man ville skulle ændre tidspunktet i hver booking. Det ville altså ikke være ressource effektivt at skulle holde data konsistent ved dette setup. 

Det bryder direkte med 2. normalform, da booking tabellen har en sammensat primærnøgle, bestående af en kombination af de unikke identifiers fra member og trainingClass, men starttidspunktet ville kun være tilknyttet trainingClass og ikke direkte have noget at gøre med member.

Eksempel på brud på 2. normalform:

![alt text](NF-examples/nf2_invalid.png)

#### 3. Normalform

Som udgangspunkt er 3. normalform overholdt, men overholdelsen af i de initielle udkast til modellen (Før det blev afgjort ikke at have membership som et enum i member tabellen) fremgik både membership og price af member tabellen, hvilket ville have brudt denne normalform, eftersom price ikke er tilknyttet primærnøglen i member tabellen, men derimod er tilknyttet den type membership man har. Samhørigheden mellem de attributes er altså grunden til at price blev flyttet med som attribut i membership entiteten, da den blev oprettet. Selvom discount og paymentType forblev i member, da de er tilknyttet og potentielt er unikke per member. 

Eksempel på ikke overholdt 3. normalform.

![alt text](NF-examples/nf3_invalid.png)

### Task 3

```
MEMBER (memberNo PK, discount DECIMAL(5,2), paymentType ENUM('Subscription', 'Clip card'), membership FK)
MEMBERSHIP (type PK, price DOUBLE(100,2))
INSTRUCTOR (staffNo PK)
TRAININGCLASS (classId PK, description VARCHAR(255), startTime DATETIME, maxParticipants INT, instructorId FK, classType FK)
CLASSTYPE (type PK)
BOOKING (memberNo PK FOREIGN KEY (memberNo) REFERENCES MEMBER(memberNo), classId PK FOREIGN KEY (classId) REFERENCES TRAININGCLASS(classId), bookingDate DATETIME)

```
OBS. Booking skal have en sammensat primærnøgle bestående af fremmednøglerne fra member og traningClass. (Beskrivelse inkluderet da syntaksen måske ikke er korrekt)