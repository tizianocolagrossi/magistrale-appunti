
# Localization
## Haar cascade classifier
Is a machine learning object detection algorithm used to identity object inside imges.
This classifier is based on the **Haar Features**. Those are weak learners based on simple features (rectangular matrices with black and white pattern). Those weak classifier, those matrices ouput a single value, that is the sum of the pixel value in the white region minus the sum of the pixel inside the black region of the matrx (haar features). Those matrix can be developed in order to find simple features, like edge, line, centered object. 
The matrix is used to search for those features inside the image by sliding and changing size over the input image. But thos calculation is quite expensive in terms of calcilation power. So we use the concept of integral images in order to achive the same result but in a more convenient way from the point of view of the calculation power needed.

The **integral image** is an image that has in every pixel [x][y] the value of the sum of the upper left region taken with the pixel [x][y] as the bottom right corner. And so we can calculate from this integral image the output value of the haar features in a less time.
We can calculate the sum of a rectangilar region pixels by taking the bottom right pixel corner value in the intgral image, mins the bottom left pixel corner, plus the top right minus the top left. And so we can calculate the feature value by calculating the white region value minus the black region value. Those value then is compared with a trashold that is learned during a training phase in order to detect if there is a feature in that position where the haar feature was calculated.

Using **Adaboost** we can select a small subset of those haar features (weak classifiers) in order to build a good classifier.  AdaBoost learns a sequence of weak classifier and combines them by minimizing the upper bound for the classification error.


# System type
## Come funziona verification ( vs identification)?
Verification diffeers from identification because in verification there is a **claim** that is not present during identification. And in the system during identification the probe is compaired with all the template in the gallery, while in the verification the probe is compaired only with the template that belong to the claimed identity.

## Difference between hard and soft biometrics?
A good biometric trait need to be **universal, unique, permanent, collectable and accepted**. Universal in the sense that all people need to have the trait. Unique means that need to be hight discriminant among the people. Permanent means that this trait need to don't change in the time and need to be permanent. Collectable means that need to be relatively easy to collect the sample and the measures of that trait. And finally the trait need to be accepted in the sende that the people should not have any objection to allowint the collectionof the trait.

An **hard biometric trait** is a trait that is **universal, unique, permanent, collectable and accepted**. Instead in the **Soft biometric trait** we remove the need of the **unique** requisite.

## Which biometrics trait is unique?
There are many of biometric traits that are unique. For example:
- DNA
- Iris
- Retina
- Fingerprint
- Ear
- Face

## Which is different between genotypic and randotypic caratteristics
The difference from genotypic and randotypoc is that the genotypic characteristic it depend from the genome of a person, and so there may be some similarityes from families.
Instead a randotypic trait does not have any connection with the genome and are truly randomic. Like iris textures or fingerprints.

## If we have two characteristics, one strong but computationally slow and one soft but faster? What can we do?
We can use the fast and soft characteristic to reduce the set of possible template from the gallery that can match the probe. And then use the string but slower characteristic to perform the final matching having a smaller set of possible candidates idendities.

## what sensors should there be to use a smartwatch for recognition in addition to the gyroscope? 
Accelerometer in order to have also the orientation of the devices. For example using it in order to try to recognize the walking of the people.


# Evaluation
## Che cosa si intende per detection and identification rate?
**(In the Identification Open Set).**
DIR(t,k). The DIR (detection and identification rate) represent the probability of a correct identification at rank k with a threshold t.  (the correct subject is returned at position k).
The probability of false reject expressed as 1-DIR(at rank 1), so FRR(t) = 1 - DIR(t, 1). 

## In quanti modi posso effettuare un identificazione? come valuto i casi corretti di accettazione? anche li devo calcolare un rate, cosa c’è al denominatore del dir?
We can perform identificationin open and closed set. The main difference is that in closed set we assume that each probe appartain to a person in the gallery, and so we cannot have Impostors in this kind of identification, while In open set we can see the impostors the people that not appartain to the gallery.

**On the dominator we have cardinality of the set PG, so the set of probes belonging to the gallery.**


## False Rejection come me lo calcolo? Nella identificazione closed set?
False rejection (FR) in the identification closed set is calsilated when the identity of the probe returned from the sistem is not the right one (is not strictly FR but kinda)

## Evaluation in Verification systems and EER metrics
In the Verification a person claims an identity and submits its sample as a probe; the
system has one or more of its templates in the archive; matches this or those
templates with the incoming template; before decide if that person is who he claimed
to be, the system compare the similarity compared by the matching with the
acceptance threshold (this threshold is decided in advance, when designing the
system); if the similarity measure meets the acceptance threshold, then the claimed
identity is accepted, otherwise it is rejected as an impostor.

From the point of view of the identities, it is a one-to-one matches: we only match the incoming template with the single or the multiple templates in the gallery that belong to the claimed identity.

The **most common measures to compare Verification Systems** are: **plot** the **curve** produced by **False Acceptance Rate** with the **curve** produced by the **False Rejection Rate**. 

an important operating point that is called **Equal Error Rate** (ERR, it is given by
that threshold where the FAR is more or less equal to the FRR)

Receiving Operating Characteristic Curve (ROC) that plots a kind of general behavior of the
system (it has FAR on the x axis and 1-FRR on the y axis; higher is the curve towards
the left upper half of the quadrant of the coordinates, better is the system).

The point provided by the threshold where FAR and FRR achieved a similar value is
called Equal Error Rate (EER). The EER is not a threshold but it's the value that we
achieved setting up a similarity threshold where the FAR is equal to the FRR.

## Perché preferiamo un basso False Acceptance Rate rispetto a un basso False Rejection Rate?
We mainly focus on False Acceptance when we have security related application. while the False Rejection may only be disturbing and little bit frustrating for the user, the False Acceptance may be dangerous in many situation because, for example, it may allow the access to a terrorist or a criminal. If we raise alarms for a false rejection, we can create panic, so also FR is important. In general, FA is the most critical situation but the criticality depends on the kind of applications.


# Spofing/Camuflage
## Come si valuta un sistema di anti-spoofing? È possibile valutarlo insieme al riconoscimento? Come?
## Evaluate spoofing together with recognition?
## What is spoofing?
## Differenza tra spoofing e camufaggio?
## Tutti i tratti biometrici sono soggetti a spoofing?
## Liveness Detection per anti-spoofing
## Tecniche che vengono utilizzate per alcuni tipi di Liveness Detection, Come si fa micro-texture analysis? operatore per la micro-texture ? Dove si trova moiré pattern?
## What is spoofing? what is very simple way to challenge paper and video spoofing?
## How you can detect moiré pattern?
## spoofing attack false acceptance rate cosa significa? cosa ci metto al denominatore? (numero di sample contraffatti)

# Face 
## Training in face recognition, Quali sono le buone regole per il training?
## Cosa è un Morphable Model?
## Differenza un sistema di face detection basato su Adaboost e quelli basati su determinate caratteristiche del volto?
## possiamo usare dei modelli 3d per il volto, quali sono le strategie migliori per utilizzarli? Se voglio continuare ad avere in input modelli 2D ma nella gallery ho modelli 3D, come posso utilizzarli nel riconoscimento?
## In che modo la posa influenza? (posizione, illuminazione) abbiamo dei metodi per valutare la posa del viso? (tramite i 3 assi) quale rotazione distorce in modo irreparabile? (abbassare la testa)
## Problem of 2D recognition, what can be solved with 3D recognition? what can't be solved by either of them?
## Qual è la principale differenza tra PCA e LDA?


# Iris
## Rubber Sheet Model
## Quali sono i pro e contro nel riconoscimento dell’iride, tra la cattura delle immagini nella banda near inference in e con la luce visibile (luce naturale e infrarossi)?
## Quali sono i problemi che possono sorgere nel riconoscimento dell'iride?

# Fingerprint
## Indice di Poincarè
## What is the other biometric feature (beyond the iris) that has random traits?
## Fingerprint Recognition
## Cosa sono le impronte latenti? primo problema tra registrare un'impronta latente e quella diretta?
## Fingerprints Recognition? Lists some types of fingerprint shapes?
## Different level of Fingerprint recognition? (micro and macro features) There is 3 level, what is the third?
## Cos’è una directional map?
## Cos’è il crossing number? A cosa serve?
## Qual è il metodo per valutare la distanza tra due minuzie?

# Ear

# Multibiometric
## Approccio multi biometrico? Come mettere insieme i risultati? Quali tipi di fusione esistono oltre a quelli basati su score? Feature level? quali sono i possibili problemi?
## Borda count quando viene utilizzato, in che metodo? fusion strategy? Utilizzare un sistema di Ranking a livello di score fusion? Vantaggi e svantaggi rank?
# Multi biometric system, how merge the result?

# Doddington
## Doddington Zoo? Estensione a cosa è dovuta? Come si potrebbe irrobustire un sistema biometrico con una gallery di soggetti?
## Si ricorda il Doddington Zoo? Come cercare di ridurre il tasso di errore causato da questi utenti, senza toccare il threshold? (abbasso la soglia solo per questi utenti)