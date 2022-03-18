
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
## What is spoofing?
A spoofing attack is a situation in which one person or program successfully masquerades as another by falsifying data (IP address, e-mail address, etc.) thereby gaining an illegitimate advantage.

Biometric spoofing is the act of fooling a biometric application, by using a copy or performing an imitation of the biometric trait that is used by that system in order to legitimately authenticate a user.

## Differenza tra spoofing e camufaggio?
We have to also distinguish Biometric Spoofing from Camouflage or Disguise. In the case of Camouflage the attack is carried out presenting an artifact biometric trait to the system to fool it pretending not to be oneself. So in that case we don’t want to be recognized. If we introduce extraneous elements on the face we may have that the detection process fails.

## Come si valuta un sistema di anti-spoofing? È possibile valutarlo insieme al riconoscimento? Come?
## Evaluate spoofing together with recognition?

One possibility is to exploit the Spoof False Acceptance Rate (SFAR): the number of the spoof attacks that are falsely accepted.

When we come to spoof detection, we've to add the Spoof False Acceptance Rate to compare it with the False Rejection Rate.

In this case False Rejection doesn't refer to the miss-recognition of a sample, but to misclassification of the genuine sample as a spoofed attack.

For the evaluation, we've to distinguish a Licit Scenario from a Spoof Scenario,
because in the first one we have the possible errors that the system can make and so
we've False Acceptance Rate (FAR) and False Rejection Rate (FRR) that can be also
summarized in Half Total Error Rate (HTER) that is computed for each threshold. In
particular, we will look for that specific operational point that is the Equal Error Rate
(EER).

**When we go to the Spoof Scenario**, we still have the False Rejection Rate because
we still have possible genuine users that can be rejected due a classifier error;
however, in this case, we can also consider a **cumulative Spoof False Acceptance Rate (SFAR)**.

In the Licit Scenario, the False Acceptance Rate can be due by the so called zero
effort attacks (a person claims an identity without taking any particular actions in
order to resemble the genuine user). 

On the other hand, in the Spoof Scenario, we
have to consider not only the zero effort attempts, but also attempts that are voluntary
carried out in order to try to reproduce the appearance of the attacked person.
In some case we can fuse recognition and anti-spoofing in a single system with a
single Accept-Reject response. So we can combine a Biometric Classifier with a
Binary Classifier, because an Antispoofing Classifier is not but a system algorithm able
to decide whether a probe is genuine or not.

In the case of the Countermeasure we can also measure the False Living Rate (FLR)
and the False Fake Rate (FFR), that in some sense substitute what we have defined
as False Rejection Rate and Spoof False Acceptance Rate.

The **False Living Rate** is the **percentage of spoofing attacks misclassified as real**, while the **False Fake Rate** is the **percentage of real access misclassified as fake**.

## Tutti i tratti biometrici sono soggetti a spoofing?
Those who are based on appearance do. Gait, key typing, the way you sign, etc., no.

## What is spoofing? How it is carried out? Which are the strategies?
Biometric spoofing attack is the act of fooling a biometric application, by using a
copy or performing an imitation of the biometric trait that is used by that system in
order to legitimately authenticate a user. In order to successfully carry out a
presentation attack, it is necessary to know which is the biometric trait or which are
the biometric traits underline the authentication process.

In Biometric Spoofing we consider different kinds of attack, where the attacker
actively acts in order to acquire the appearance of the attacked person.
A biometric system can be attacked in different points: in the transmission
channel; in the feature extractor; in the comparator. It’s possible to inject data over of
each of such channel in order to force the result that we want. It is also possible to
attack the Database with templates that don't belong to enrolled people. Especially
when there is an automatic update of the gallery, in order to address the change of
appearance of a person, it is possible to "poison" the sub-gallery for that subject by
injecting images.

All of these kinds of attacks along the channels, the database and so on and so forth,
can be considered as Indirect Attacks. Defending from these kinds of attacks is a
matter of cybersecurity.


Another kind of attacks are the Direct Attacks. In this case, our final aim is not to
recognize the person, but rather to detect the attack so that we can proceed with a
countermeasure.
We can do a Classification of spoofing attacks: 2D spoofs attack and 3D spoofs
attack.

In the 2D Spoofs the attack is carried out by using a 2D surface like a photo or like a
video that is played once we have somehow recorded the attacked person. Photo
attacks can use either hard copy, hard copy with a eye whole, screen. An alternative
for 2D spoof is to exploit Video Attacks. In that case we recorded the person that we
want to attack using different screen resolutions and then present this video instead of
our true face. This is also called "Replay Attack”.

In the 3D Spoofs the attack requires that there is not a plain surface but a three
dimensional surface and in general masks are used for this kind of attack (Mask
attacks).

For a human operator, it's trivial to detect such kind of spoof attack; especially when
the image that is shown to the system does not occupy the overall screen.
An automatic system is designed in order to identify region of interest containing face;
since in this case the region of interest containing the face is present, there are all the
things which automatic system cares about. Unless we extend the functionality of the
system with an anti-spoofing procedure, it is quite easy to carry out this kind of
attack.
For each kind of attack there are different anti-spoofing techniques that are mostly
based on liveness detection.

 In general, what we try to do, a part from studying the
reflection pattern of the surface and the microtexture, is to "challenge" the user in
order to detect the kind of reaction of the face that we've in front.
We've anti-spoofing techniques that work at Sensor-level (Hardware Based); in
practice they exploit the intrinsic properties of a living body including the kind of
reflectance that is producing and involuntary signals (for example micr movements of
the eyes) that are completely absent in a photo or in a mask. An example of antispoofing technique that takes advantage of these kind of movements is Eye Blink. In
fact, the eye blinking is a natural involuntary movement that can be detected in order
to distinguish a real person from a photo.
An the other hand, we can have a voluntary response from the so called “ChallengeResponse” strategy. In this case we can ask to a person that is presenting a probe
to make a certain kind of expression or movement. If what we expected doesn’t
happen on the face that is shown to the system, we can conclude that this is a spoof
attack.
Other kinds of anti-spoofing techniques are carried out at Feature Extractor level
(Software based) and they can be either static or dynamic.
An example of study static is the study of the microtexture of the acquired image; with
dynamic we study the kind of dynamic features that a certain movement can produce
when it is naturally carried out.
We can have also a Score level fusion where we can either fuse anti-spoofing and
recognition in a single module or carry out anti-spoofing in advance and proceed with
the recognition only if the anti-spoofing returns a genuine response.


## Anti-spoofing on the face
One of the most intuitive approaches to 2D Print Attach is Liveness Detection. In
practice, the essential difference between the live face and photograph is that a live
face is a fully three dimensional object while a photograph could be considered as a
two dimensional planar structure. This means that we can use structure from motion
to derive kind of depth information to distinguish a live person from a still photo.
The disadvantages of depth information are: it is hard to estimate depth information
when head is still (we have to ask for a movement, otherwise it is very difficult to carry
out a liveness detection in this trivial way) and the estimate is very sensitive to noise
and lighting (if we’re in a dark environment, this may help the attacker to hide some
feature change with the respect to a real face).
It is possible to compute the optical flow (this is a technique that tries to extract the
motion vector by comparing the position of each pixel in one frame and in the
following one) on the input video to obtain the information of face motion for liveness
judgment, but it is vulnerable to photo motion in depth and photo bending.
A possible multimodal approach fuses face-voice against spoofing exploiting the lip
movement during speech.
Among the earliest approaches, it is possible to consider those relying on eye blinking
analysis because it relies on the natural pattern that is produced on a regular basis in
eye dynamic. Eyeblink is a physiological activity of rapid closing and opening of the
eyelid and it’s a movement that cannot be avoid at all.
An eyeblink activity can be represented by an image sequence consisting of images.
The typical eye states are opening and closing. In addition, there is an ambiguous
state when blinking from open state to close or from close state to open. It is possible
to define a three-state set for eyes, Q = {α : open, γ : close, β : ambiguous}. A typical
blink activity can be described as a state change pattern of α → β → γ → β → α.
An other possibility is based on Micro-texture analysis. This is especially efficient
when we have 2D Print attacks or Photo attacks because any kind of photographic
paper or printing paper has a kind of micro-texture that can be not visible by eyes but
can be detected by using texture features.
Human faces and prints reflect light in different ways because a human face is a
complex non rigid 3D object (so it reflects light in different directions) whereas a
photograph is a planar rigid object (different specular reflections and shades). The
surface properties of real faces and prints, e.g. pigments, are also different because
natural pigments are different from ink pigments. These last ones contain some
metallic components so this affects the way light is reflected. The work exploits multiscale local binary patterns (LBP). The strategy that is used in multi-scale local binary
patterns is more or less the same but much more simpler to adopt than the bank of
wavelets with different frequencies. Multi-scale local binary patterns are computed by
using windows of different size. As a further advantage, the same texture features that
are used for spoofing detection can also be used for face recognition. The vectors in
the feature space are then fed to an SVM classifier which determines whether the
micro-texture patterns characterize a live person or a fake image.
Another important approach is the Captured-Recaptured approach. We must
consider that all the distortions that are present when we capture an image affect the
face image when it passes through the camera system. Such distortions are applied
twice when we take the photo of a person and then when we print it. In practice,
when we use a photo taken by a photo, like it may happen when using a photo taken
on the internet, we have a much lower image quality compered to the capture of a real
face image.
Another approach is the Gaze Stability. There is an algorithm proposed by Ali et al.
that is based on the assumption that the spatial and temporal coordination of the
movements of eye, head and (possibly) hand involved in the task of following of a
visual stimulus is significantly different when a genuine attempt is made compared
with certain types of spoof attempts.
In a challenge response strategy, when the system asks us or to the genuine
subject to rotate the head, the kind of temporal coordination between the movement
of the eyes, the movement of the head (if they are possibly involved) is different when
if the person is looking at the screen through the holes in a mask.
The task, or the so called "challenge", requires head/eye fixations on a simple target
that appears on a screen in front of the user.
In the case of a photograph spoofing attack, visually guided hand movements are
needed to orientate the photo to point in the correct direction towards the challenge.
So the temporal coordination between the eye fixations and the head rotation is
somehow influenced by this hand movement that it is not present in a genuine
presentation. The Optical Flow Correlation (motion correlation) takes into account
the movement of the head of the user trying to authenticate and the movement of the
background of the scene, because if they move together means that there is a
spoofing attack, because face and background move together while they should not
move together. Optical Flow doesn't work in two cases: one case in the case of
masks, but also if we have a bended face image over the attacker face.
Another possible approach is to use the Image Distortion Analysis. It is based on the
kind of specular reflections that are produced by a printed paper surface or LCD
screen (a smartphone screen for example) during capture. There is a kind of
compound analysis that is carried out according to these different elements and then
it is possible to concatenate the features extracted from each of this image distortion
factors and then to train the ensemble classifier.
Another way to effectively use the study of microtextures is related the Replay Video
Attacks. When we carry out a Replay Video Attack, we show a video on another
screen. In this case there is a special kind of pattern that is called moiré pattern
aliasing that is caused by the overlay of two pixel patterns: one from the original
video and the other one from the screen over which the video is shown.
It is possible to detect this special pattern using Multi-scale LBP and DSFIT (this
tries to detect the features with the highest energy in the image). In practice, whenever
a kind of moiré pattern is present, we can conclude that we’re in presence of a replay
spoof attack.

## How you can detect moiré pattern?
It is possible to detect this special pattern using Multi-scale LBP and DSFIT (this
tries to detect the features with the highest energy in the image). In practice, whenever
a kind of moiré pattern is present, we can conclude that we’re in presence of a replay
spoof attack.

## spoofing attack false acceptance rate cosa significa? cosa ci metto al denominatore? 
Come similar to FAR but now in the denominator we have the number of samles spoofed.

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