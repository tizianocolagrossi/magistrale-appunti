
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
the eyes) that are completely absent in a photo or in a mask.
 An example of antispoofing technique that takes advantage of these kind of movements is Eye Blink. In
fact, the eye blinking is a natural involuntary movement that can be detected in order
to distinguish a real person from a photo.
An the other hand, we can have a voluntary response from the so called “ChallengeResponse” strategy. 
In this case we can ask to a person that is presenting a probe
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
natural pigments are different from ink pigments. 
These last ones contain some metallic components so this affects the way light is reflected. 
The work exploits multiscale local binary patterns (LBP). The strategy that is used in multi-scale local binary
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
When we choose the training set, templates of **different quality** must be included in
the training set, because it **must include as many as possible different conditions** that
will be found in the testing set or in the real life.
The generalizability of outcomes, that is the possibility to get the same kind of
performance even with unknown data, depends on the choice of the training set.
Dataset must considering distortions (**PIE: Pose, Illumination, Expression**; etc...) and
having **good positive samples and good negative samples** (something that seems
a face but it is not a face).

We may train the algorithm over a different kinds of objects or even refine the
behaviour of the existing one since we can even re-train the system starting from the
last results published in the available models. If we're training the algorithm in order to
detect faces, we must have a huge amount of faces over which the training can be
carried out. If we're looking for mouth, then we will have a huge amount of images just
centered on the mouth or cropped over the mouth.

The Positive Examples help the system to learn which are the stereotypical elements
that make up a face (in the case of face detection).
The Negative Examples must be at least as many as the Positive ones; these are not
samples that contain the object that we're looking for but they can fool the system. So
they are samples that could be wrongly classified.
We must stress the training of the system by having the system learn from the images
that can fool it.

## Cosa è un Morphable Model?
Expression still affects face recognition in 3D. It's possible to exploit the so called
**morphable model, that can be somehow distorted in order to reproduce any kind of expression**. 
But the computational complexity increases in a dramatic way.
The model is called morphable model because we **can change some relationship**
**among polygons in the model** in order to create different appearances of the same
face or also to create different faces. Shape and texture of the generic model are
manipulated to adapt to the captured images.
**Morphable models also allow to synthetize face expressions** approximating the
possible expressions of a specific subject.
The morphable model is based on a data set of 3D faces. Morphing between faces
requires full correspondence (alignment) between all of the faces.

## Problem of 2D recognition, what can be solved with 3D recognition? what can't be solved by either of them?
Regarding the Pose variations, while 2D recognition can be affected by rotation,
pitch and yaw variations in one pose, this is not true with 3D. When we carry out a
recognition strategy based on 3D we actually don't use a 2D/flat image but we use a
3D model and so we can test with different poses of this 3D model because it can be
rotated and its pose can be also changed according to pitch and yaw so that we can
try to have the 3D model assume the same pose represented in the 2D image.
For a similar reason also Illumination problems can be considered as solved
because, as in the same way, we can rotate the model and also synthetize different
illuminations.
While Expression still affects face recognition in 3D. If we think especially on
exaggerate expressions that are the ones more difficult to capture even in 2D, they
still create problems in 3D because they can cause a real modification with the
respect to a neutral 3D model.
Also Aging can affect 3D strategy. According to an increasing age, it's possible that
some face issues relax. Since the volume of a certain anatomic part can increase with
age, this affects 3D strategy too.
Makeup doesn't affect 3D model at all because only considering the geometric
relationships, the 3D volume determined by the face doesn't change.
Plastic Surgery can affect 3D models according to the kind of weight and the
extension of the intervention.
Occlusion affects 3D as it affects 2D, because when we build the model for the probe
and it wears a scarf or glasses or whatever, this model is poorly matched against the
enrolled one.
We can summarize the pros and cons of 3D strategies by saying that: in 3D we have
much more information, the built models are much more robust to a number of
distortions, there is the possibility to synthetize (approximate) 2D images from virtual
3D poses and expressions computed from a 3D model (PROs); the cost of devices,
computational cost of procedures, possible risk that some acquisition devices can
present, for example the laser scanner is dangerous for the eye (CONs).


# Iris
## Rubber Sheet Model
The Rubber Sheet Model takes into account the problem of axis and especially the
problem of having a **pupil that is not perfectly centered within the iris**. Determining the
right centre for the polar coordinates is of paramount important but pupil and iris are
not perfectly concentric and **size of the pupil can change** due to illumination or
pathological conditions (drunk or drugs).

**Taking a fixed number of points on each radius that is contained between the pupil**
**boundary and the iris boundary, it's possible to normalize the deformed distance:**
when we have a larger region, the sampling will be less dense; we will have a more
dense sampling in the narrower region. At the end, we will be able to obtain a
representation that represents the ideal iris with the pupil perfectly centered.
The model maps each iris point into polar coordinates where the center of the polar
coordinates is the center of the pupil. The transformation is not a simple polar
transformation because the new coordinates for each point are a linear combination
of set of points that are respectively the coordinates of the pupil contour and the
those of the external iris contour.

The model compensates for pupil dilation and size variations by producing and
invariant representation. **The model does not compensate for rotations**. 
**However, during matching, in polar coordinates, this is done by translating the obtained iris template until alignment.**
Once the procedure has extracted the bend containing the iris and once that bend
has been normalized, then Daugman applies Gabor filters in polar coordinates in
order to obtain the so called iris code.

## Quali sono i pro e contro nel riconoscimento dell’iride, tra la cattura delle immagini nella banda near inference in e con la luce visibile (luce naturale e infrarossi)?
**PRO**

The iris is visible yet well protected, so as the resolution of capture equipment increases
then also the ability to capture this stricter distance increases and so we have a good
advantage of using this extremely distinguishes trait.

It is a time invariant (after about 2 years age) and extremely distinguishing trait (right
different from left and even twins have different irises).
Its image can be acquired without direct contact, so it is well acceptable not like the
retina fundus that requires to touch the eye surface with the capture device.
It can be acquired in both near to infrared and visible wavelengths.

**CONS**

Iris’ surface is very limited: only about 3.64 cm2, so we need a very good capture device
in order to obtain a good image of the iris. A “good” acquisition requires a distance of less
than one meter to guarantee a sufficient resolution, depending on the input device.
Possible problems are represented by: the small size; the high resolution required by the
equipment; the limited depth of field so that we need to pay attention to the focus and out
of focus problems; we need to align with optical axis unless we use some tricks like the
so called "off-axis problem" (if the person is looking in another direction, the iris will not
be exactly in the center of the sclera, but it will move towards the right or the left ends of
the sclera); the specular reflections; the possible presence of glasses or contact lens.

The two **main capture modalities are the Visible light and the Infrared light**.

In Visible light we have the melanin that absorbs the visible light, so
we can clearly see the different colors that may appear in the iris.
The layers that make up the iris are well visible. But the image
contains noisy information on texture.

In Infrared light the melanin reflects most of the infrared light, so
that we don't have color informations. The texture is more visible but
it requires special equipment.

In the visible band of light, the iris reveals a very rich, random,
interwoven texture (the “trabecular meshwork”).
Trabecular meshwork is basically due to muscles.
In infrared illumination even dark brown eyes show a rich texture,
that it could not be easy visible with Visible light.


# Fingerprint
## Indice di Poincarè
A directional map is vector field (a region filled of vectors
with different orientations). Our fingerprint is a curve immersed in this vector field and so,
the Poincaré index, is defined as the total rotation of the vectors of this field once we
travel along the curve.

The index of Poincarè can be calculated as follows:
- The index PG, C (i, j) is calculated by algebraically adding the differences in orientation
between adjacent elements of C (curve).
-  The sum of the differences of orientations requires to associate a direction to each
orientation. One can randomly select the direction of the first element and assign the
direction closest to that of the previous element to all subsequent elements.
- It is shown that, for closed curves, the Poincare index assumes only one of the discrete
values 0 °, ± 180 °, ± 360 °. In particular, regarding the singularities of fingerprints: 0 no singularity, +180 loop -180 delta +-360 whorl.

## Cosa sono le impronte latenti? primo problema tra registrare un'impronta latente e quella diretta?
The latent fingerprint are the fingerprints that we leave on any suitable surface when we touch it.
They are collected for example during investigations by using a special powder. In this case we’ve to compare a
fingerprint that is usually complete (the enrolled fingerprint in the gallery) with a latent
fingerprint that usually is a fraction, fragment of the complete fingerprint. So that there is
the problem of the Alignment.

## Fingerprints Recognition? Lists some types of fingerprint shapes?

A fingerprint recognition application follows the same workflow followed by experts in the
analysis of fingerprints. They take into account several factors that can be also organized
into a hierarchical tree of decision, so that first of all one looks at the accord in the
configuration of the global pattern, so whenever the global pattern is available we first
check that the fingerprints to match have a common typology of the global pattern. This
also entails, in the case of the latent fingerprints, that we have a fragment large enough to
possibly present the global pattern. In general the global pattern is quite wide enough to be also visible in latent fragments.
Then we look for a qualitative accord, that implies that the corresponding minute details
are identical. In the sense that, in a certain region, it's possible to find out a bifurcation,
together with an endpoint and so on and so forth.
There is also a quantitative factor because there are a minimum number of minutiae
details that must match between the two prints.
There must be also a deep level of correspondence of minute details because they must
be identically interrelated, in the sense that it's not sufficient that in a certain region of the
fingertip we found for example two bifurcation but for example they must be divided from
each other by the same number of ridges.

METHODOLOGICAL APPROACHES 

There are different methodological approaches but they can be classified into three main
classes:
- matching based on **correlation**: it is possible to compute the correlation among two
images, in this case two finger images, by superimposing those and then by computing
of the correlation between the corresponding pixels.
The first problem with this method is the Alignment. It could not be immediate to
determine, especially in automatic procedure because the matching based on
correlation must be iterated for different alignment, until the best one is found.
This kind of computation is sensitive to non linear transformations for example those
caused by the shift of the finger when the fingerprint is captured.
These is also a high computational complexity. This is mostly used for global
characteristics because in practice it is a complete matching that considers all the
possible aspects, in particular the macro characteristics of the fingerprints.

- matching based on **ridge features**: the extraction of minutiae in fingerprint images of
low quality is problematic, therefore these methods use other features such as ridges
orientation and local frequency, shape of ridges and texture, which are more reliable
and easier to extract, but also less distinctive.
This kind of method has a low discriminating power but it can be used as a first step
in the flow in order to cut the search or to arrive more quickly to a decision.

- matching based on **minutiae**: the minutiae are first extracted from the two fingerprints
and stored as two sets of points in a two dimensional space. In this case we've the
problem of the Orientation. To find out which is the best pair of the extracted minutiae,
we must take into account that, due to temporary problems with sensors, we may also
have pairs of images where in one or in the other there are some lack in minutiae.
In general the method searches for the best alignment between the two sets (the one
that maximized the number of corresponding pairs of minutiae). After this we can also
go into deeper details by measuring the interrelation that is if they appear in a similar
pattern relating to the ridges over which they appear, their orientation, the distance
between the minutiae, and so on and so forth.

Problems with fingerprint matching:
- Scarce overlap, for example it's possible that a finger is not well centered on the
sensor and so we may have a completely different framing. We've to consider only the
common part, so we try to align the common parts of the two images.
- Different skin conditions, for example a dry skin can hinder a correct capture of the
fingerprint.
- Non-linear distortion due to a different pression.
- Too much movement and / or distortion.
- Non-linear distortion of the skin because we’ve a three dimensional structure that can
be modify by the elasticity of skin in relation with the pressure so that if we acquire the
same fingerprint in two different sessions, if we apply a different pressure they may
appear. Slightly different.
- Variable Pressure and skin conditions.
- Errors in the extraction of the features because feature extraction algorithms are
imperfect and often introduce measure errors, particularly with footprints of low quality.

## Different level of Fingerprint recognition? (micro and macro features) There is 3 level, what is the third?
- Features, loop delta whorl
- Minutiae
- ridge porus

## Cos’è una directional map?
A directional map is
vector field (a region filled of vectors with different orientations). Our fingerprint is a
curve immersed in this vector field.

## Cos’è il crossing number? A cosa serve?
It is used to detect minutiae.
The crossing number is the number of changes in color that happen in the neighborhood
of the pixel that is take into account in that moment.
We compute for each pixel this crossing number in order to determine whether this pixel
represents a minutiae or not. 

but before proceding with the crossing number we need to do:
- Binarization procedure in order to convert a gray level image into a binary image
(histogram analysis is used but it is less trivial than supposed);
- Thinning procedure that transforms any ridge that is thicker than 1 pixel to a 1 pixel
thickness, so lines of 1 pixel.
- So once we've black and white images with a thinning of ridge traces we can proceed
to scanning such ridges in order to locate pixels that correspond to minutiae

## Qual è il metodo per valutare la distanza tra due minuzie?
Ridge count, so the count of how many ridges are between two minutiae.

The ridge count is an abstract measure of the distance
between any two any points of a fingerprint. Given two
points a and b of a fingerprint, the ridge count is the number
of ridge lines intersected by the segment ab.

This cannot be done for each pair of minutiae because
endpoints are not reliable enough (they can be caused by an interruption of one ridge due
to bad thresholding or whatever).


# Ear

# Multibiometric
## Approccio multi biometrico? Come mettere insieme i risultati? Quali tipi di fusione esistono oltre a quelli basati su score? Feature level? quali sono i possibili problemi?
## Borda count quando viene utilizzato, in che metodo? fusion strategy? Utilizzare un sistema di Ranking a livello di score fusion? Vantaggi e svantaggi rank?
# Multi biometric system, how merge the result?

# Doddington
## Doddington Zoo? Estensione a cosa è dovuta? Come si potrebbe irrobustire un sistema biometrico con una gallery di soggetti?
## Si ricorda il Doddington Zoo? Come cercare di ridurre il tasso di errore causato da questi utenti, senza toccare il threshold? (abbasso la soglia solo per questi utenti)