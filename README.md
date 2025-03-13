# OLA2_DB

### Task 1

Herunder finder man vores Entiteter og ER-Diagram

![Entities](Entities.png)
![ER-Diagram](ER-Diagram.png)

Ved udarbejdelsen af vores ER-Diagram overvejede vi om vi skulle have vores membership som ENUM, og på den måde ikke have en ekstra entitet, men vi ville ikke kunne placere price attributen i member, da det ikke ville overholde 3. Normaliseringsform. Udover dette ville besluttede vi at det ud fra et spørgsmål om vedligeholdelse ville give bedre mening at at gøre membership til en entitet, da det vil være lettere for en administrativ medarbejder at tilføje eller fjerne forskellige typer medlemskaber på denne måde, i modsætning til hvis det var et enum, hvorved en teknisk medarbejder ville være nødt til at ændre i selve modellen, hvis ændringer skal implementeres.

Vi overvejede også at lave vores ClassType (Under TrainingClass) til en ENUM, og besluttede af lignende hensyn til membership at oprette classType i stedet, for at håndtere fremtidige ændringer.

Udfra opgavebeskrivelsen mener vi at Discount skulle være en attribut hos den enkelte member, da der i opgavebeskrivelsen står "Medlemsrabatter: Kan gives i særlige tilfælde", hvilket hentyder til personlige rabatter, som ikke tilbydes i hele centeret. Dette kunne forekomme ved en aflyst træning eller andet.

Vi har valgt at lave en PaymentType i vores Member entitet, som indeholder fx Månedsvis abonnement eller klippekort.
Dette overvejede vi at separere i sin egen entitet kaldet "Payment", som ville indeholde paymentType, Discount og Price. 

Ud fra domænebeskrivelsen opstod der tvivl om det var betalingsdetaljer, eller faktiske betalinger, f.eks. en faktura der var tale om. Vores model er altså lavet med henblik på at holde betalingsdetaljerne, hvorefter det så var tanken at man kunne bruge en stored procedure eller function til at udregne de faktiske opkrævninger, som efterfølgende ville blive gemt i en anden tabel eller database. 

Skulle det derimod være hensigten med domænet at det var opkrævningerne, der var tale om. Ville modellen have set anderledes ud, og følge overvejelsen fra tidligere, hvor en payment entitet var oprettet, til at holde data, som price, discount, paymentType, have en relation til et medlemsskab og en due date / payment date. 

### Task 2

##### 1. Normalform

Første normalform er overholdt allerede ved udarbejdedelsen af entiter og ER-Diagram, da alle vores værdier er atomare.

##### 2. Normalform

Også 2. normalform var overholdt ved udviklingen af entiteter og ER-Diagram, men skulle der kigges på en måde at bryde 2. Normalform kunne man eksempelvis rykke startTime (fra TrainingClass) til Booking entiteten.

Dette ville give nogle udfordringer fordi starttiden vil gentages for alle bookinger tilhørende den samme trainingclass, hvilket ville gøre det besværligt at ændre start tidspunktet, eftersom man ville skulle ændre tidspunktet i hver booking. Det ville altså ikke være ressource effektivt at skulle holde data konsistent ved dette setup. 

Det bryder direkte med 2. normalform, da booking tabellen har en sammensat primærnøgle, bestående af en kombination af de unikke identifiers fra member og trainingClass, men starttidspunktet ville kun være tilknyttet trainingClass og ikke direkte have noget at gøre med member.

### Task 3

```
MEMBER (memberNo PK, discount DECIMAL(5,2), paymentType ENUM('Subscription', 'Clip card'), membershipType FK)
MEMBERSHIP (type PK, price DECIMAL(10,2))
INSTRUCTOR (staffNo PK)
TRAININGCLASS (classId PK, description VARCHAR(255), startTime DATETIME, maxParticipants INT, instructorId FK, classType FK)
CLASSTYPE (type PK)
BOOKING (bookingId PK, memberNo FK, classId FK, bookingDate DATETIME)

```
