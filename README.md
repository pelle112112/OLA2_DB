# OLA2_DB

### Task 1

Herunder finder man vores Entiteter og ER-Diagram

![Entities](Entities.png)
![ER-Diagram](ER-Diagram.png)

Ved udarbejdelsen af vores ER-Diagram overvejede vi om vi skulle have vores membership som ENUM, og på den måde ikke have en ekstra entitet, men vi ville ikke kunne placere price attributen i member, da det ikke ville overholde 3. Normaliseringsform.

Vi overvejede også at lave vores ClassType (Under TrainingClass) til en ENUM, og dette ville også være en fin løsning, men vi var usikre på senere modificering af databasen ved tilføjelse af nye typer af training classes.

Udfra opgavebeskrivelsen mener vi at Discount skulle være en attribut hos den enkelte member, da der i opgavebeskrivelsen står "Medlemsrabatter: Kan gives i særlige tilfælde", hvilket hentyder til personlige rabatter, som ikke tilbydes i hele centeret. Dette kunne forekomme ved en aflyst træning eller andet.

Vi har valgt at lave en PaymentType i vores Member entitet, som indeholder fx Månedsvis abonnement eller klippekort.
Dette overvejede vi at separere i sin egen entitet kaldet "Payment", som ville indeholde paymentType, Discount og Price. Vi mener dog at en bedre løsning ville være at separere betalinger i en helt anden tabel, og så håndtere dem ved triggers der tager priser, rabatter osv og udregner fakturaer der lagres i den anden tabel.

### Task 2

##### 1. normalform

Vi overholdte 1. normalform da vi udarbejdede entiter og ER-Diagram, da alle vores værdier er atomare.

##### 2. normalform

Vi overholdte også 2. normalform ved udviklingen af entiteter og ER-Diagram. Vi kunne bryde både 1. og 2. Normalform ved fx at rykke startTime (fra TrainingClass) til Booking entiteten.
Vi bryder 1. Normalform, fordi starttiden vil gentages for flere bookinger for det samme trainingclass, hvilket ville gøre det besværligt at ændre start tidspunktet, eftersom man ville skulle ændre tidspunktet i hver booking.

Det ville bryde 2. normalform, da booking tabellen har en sammensat primærnøgle bestående af en kombination af de unikke identifiers fra member og trainingClass, men starttidspunktet ville kun være tilknyttet trainingClass og ikke direkte have noget at gøre med member.

### Task 3

```
MEMBER (memberNo PK, discount DECIMAL(5,2), paymentType ENUM('Subscription', 'Clip card'), membershipType FK)
MEMBERSHIP (type PK, price DECIMAL(10,2))
INSTRUCTOR (staffNo PK)
TRAININGCLASS (classId PK, description VARCHAR(255), startTime DATETIME, maxParticipants INT, instructorId FK, classType FK)
CLASSTYPE (type PK)
BOOKING (bookingId PK, memberNo FK, classId FK, bookingDate DATETIME)

```
