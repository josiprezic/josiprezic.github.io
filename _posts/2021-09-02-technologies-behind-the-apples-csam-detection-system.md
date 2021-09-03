---
title: Technologies Behind the Apple’s CSAM Detection System
date: 2021-09-02 00:27:00 +0545
categories: [Security]
tags: [apple,neuralhash,private-set-intersection,threshold-secret-sharing,privacy]
---

# Technologies Behind the Apple’s CSAM Detection System

**A few weeks ago Apple announced its new system that will scan iCloud for illegal child sexual abuse materials or CSAM. CSAM detection enables Apple to accurately identify and report iCloud users who store known CSAM material in their iCloud Photos accounts.**

# Introduction

Apple wants to help protect children from people who use communication tools to recruit and exploit them, and limit the spread of CSAM files. On the other side, Apple’s plan has been particularly controversial and has prompted concerns about the system potentially being abused by governments as a form of mass surveillance.

But rather than analyzing the benefits and drawbacks of this new feature, I would like to say a few words about the cryptographic techniques and protocols used for this system implementation.

# CSAM Detection Process

Before explaining these technologies, let’s step back for a moment and take a quick look at the whole process of CSAM detection and its steps to get some more context around this.

So the steps are as follows:

1. **Image upload initialization:** When iCloud Photos is turned on, photos and videos stored on iPhones are automatically uploaded to iCloud. Once a new image appears in the Photos library, it will initiate the iCloud upload process.

1. **CSAM check triggered:** The CSAM scanning system is a part of the iCloud Photos upload process and it will be triggered once the upload is initiated. Keep in mind that it does not scan private photo libraries stored on iPhone devices and it does not work for users who have iCloud Photos disabled.

1. **Hash calculation:** Before uploading, the **NeuralHash** function is used to calculate hash values of these images.

1. **Hash comparison:** The cryptographic technique called **Private Set Intersection**is used to match a database of image hashes provided by agencies like NCMEC to hashes calculated in the previous step to search for CSAM.

1. **iCloud upload:** Hash comparison results created in the previous step are saved in the form of vouchers. These so-called safety vouchers are then uploaded to iCloud Photos along with the images.

1. **CSAM threshold exceeded:** If the specified threshold (maximum number of images containing CSAM) is not exceeded, the cryptographic construction called **Threshold Secret Sharing** does not allow Apple servers to decrypt any match data, and does not permit Apple to count the number of matches for any given account. After the threshold is exceeded, Apple servers can decrypt vouchers corresponding to positive matches.

1. **Manual review process:** This is further mitigated by a manual review process wherein Apple reviews each report to confirm there is a match, disables the user’s account, and sends a report to NCMEC.

As we can see, the CSAM detection system combines three technologies:

* NeuralHash

* Private Set Intersection

* Threshold Secret Sharing

# NeuralHash

Apple defines NeuralHash as follows:

> NeuralHash is a perceptual hashing function that maps images to numbers. Perceptual hashing bases this number on features of the image instead of the precise values of pixels in the image. The system computes these hashes by using an embedding network to produce image descriptors and then converting those descriptors to integers using a Hyperplane LSH (Locality Sensitivity Hashing) process. This process ensures that different images produce different hashes.

**NeuralHash** technology analyzes an image and converts it to a hash (fixed-size string value) specific to that image content. Only another image that appears nearly identical can produce the same hash. The main purpose of the hash is to ensure that identical and visually similar images result in the same hash, and images that are different from one another result in different hashes.

Finally, it is worth mentioning that NeuralHash is not a machine learning classifier. It has not been trained on CSAM images and it does not contain extracted features from CSAM images (e.g. faces appearing in such images) or any ability to find such features elsewhere. Indeed, NeuralHash knows nothing at all about CSAM images. It is an algorithm designed to answer whether one image is really the same image as another, even if some image-altering transformations have been applied (like transcoding, resizing, and cropping). Below are a few examples used to highlight the general idea.

# Private Set Intersection

Here is the definition from the Apple’s CSAM technical summary:

> Private Set Intersection (PSI) is a cryptographic protocol that two parties use, for example Apple servers and a user’s device. Before the protocol begins, Apple and the user’s device have distinct sets of image hashes that each system computed using the NeuralHash algorithm. […] The PSI protocol ensures that Apple learns the image hashes in the intersection of the two sets, but learns nothing about image hashes outside the intersection.

Increasing dependence on anytime-anywhere availability of data and the commensurately increasing fear of losing privacy motivate the need for privacy-preserving techniques. One interesting and common problem occurs when two parties need to privately compute an intersection of their respective sets of data. In doing so, one or both parties must obtain the intersection (if one exists), while neither should learn anything about the other set.

**Private Set Intersection** technique does exactly that. It allows two parties holding sets to compare encrypted versions of these sets in order to compute the intersection. In this scenario, neither party reveals anything to the other party except for the elements in the intersection.

Except for the CSAM detection, Apple uses this technique in Password Monitoring as well. Interestingly, this technique was also used during the COVID-19 pandemic to securely create digital contact tracing applications while keeping user privacy in mind.

The Apple’s system used for CSAM Detection extends this basic PSI mechanism to support the client including additional payload data associated with each image hash, and guarantees that this additional payload is only accessible for image hashes in the intersection of the two sets. Also, the system applies PSI in conjunction with other cryptographic techniques like Threshold Secret Sharing, described in the next section. To learn more about PSI, check the [Apple PSI system security protocol and analysis](https://www.apple.com/child-safety/pdf/Apple_PSI_System_Security_Protocol_and_Analysis.pdf).

# Threshold Secret Sharing

Wikipedia defines Secret Sharing as follows:

> Secret sharing refers to methods for distributing a secret among a group of participants, each of whom is allocated a share of the secret. The secret can be reconstructed only when a sufficient number, of possibly different types, of shares are combined; individual shares are of no use on their own.

Suppose that we possess sensitive information (e.g. rocket launch codes, secret key for our shared crypto-wallet, sensitive database, etc.) and we want to protect it. Since we know that it is not a good idea to “put all eggs in one basket” and have a single point of failure, we have to look for some other, more advanced techniques used for information protection. We want to somehow split the secret and keep its pieces in a couple of different places. And that’s where the **Threshold Secret Sharing** comes in.

**Threshold Secret Sharing** is a cryptographic technique that **enables a secret to be split into distinct shares** so it can only be reconstructed from a predefined number of shares (the threshold).

So let’s say that our rocket-launching secret is split into one thousand shares and the threshold is ten. Then the secret can be reconstructed from any ten of the one thousand shares. However, if only nine shares are available, then nothing is revealed about the secret.

### But how does it impact the CSAM Detection system?

The CSAM Detection system uses Threshold Secret Sharing to protect information about images stored in iCloud Photos when the number of matching images has not crossed a certain threshold. Only once the number of matches exceeds the threshold will the secret-sharing reconstruction algorithm enable the system to learn the additional data the client included with each of the matching images. Nothing is ever revealed about non-matching images during any step of the CSAM Detection process.

# Conclusion

CSAM Detection is a new feature that will be included in an upcoming release of iOS 15 and iPadOS 15. Instead of taking the same path for the CSAM detection as other big tech companies, Apple created their own, a little bit more privacy-oriented CSAM detection system.

Although this new system is under fire these days and it aroused a controversy among the iPhone users, I think we can all agree that the technologies used for the system implementation as well as their combination and interoperability are very interesting, or at least thought-provoking.

# Sources

1. [Expanded Protections for Children — Apple](https://www.apple.com/child-safety/)

2. [Expanded Protections for Children — Technology Summary](https://www.apple.com/child-safety/pdf/Expanded_Protections_for_Children_Technology_Summary.pdf)

3. [Security Threat Model Review of Apple’s Child Safety Features](https://www.apple.com/child-safety/pdf/Security_Threat_Model_Review_of_Apple_Child_Safety_Features.pdf)

4. [Presentation at USENIX Security Symposium by Ivan Krstic and Erik Neuenschwander](https://www.apple.com/105/media/us/child-safety/2021/7bc1183f-32b5-41da-8757-f7625cf634a3/films/usenix-security-symposium/child-safety-usenix-security-symposium-tpl-us-2021_16x9.m3u8)

5. [CSAM Detection — Technical Summary](https://www.apple.com/child-safety/pdf/CSAM_Detection_Technical_Summary.pdf)

6. [Apple PSI System — Security Protocol and Analysis](https://www.apple.com/child-safety/pdf/Apple_PSI_System_Security_Protocol_and_Analysis.pdf)

7. [Technical Assessment of CSAM Detection — Benny Pinkas](https://www.apple.com/child-safety/pdf/Technical_Assessment_of_CSAM_Detection_Benny_Pinkas.pdf)

8. [Emiliano De Cristofaro and Gene Tsudik: Practical Private Set Intersection Protocols with Linear Computational and Bandwidth Complexity — University of California, Irvine](https://eprint.iacr.org/2009/491.pdf)

9. [Gilad Asharov: Threshold Secret Sharing — Bar-Ilan University](http://cyber.biu.ac.il/wp-content/uploads/2020/02/Threshold-Secret-SharingToPublish.pdf)

10. [wikipedia.org](https://www.wikipedia.org/)

11. [KhaosT/nhcalc](https://github.com/KhaosT/nhcalc)

12. [AsuharietYgvar/AppleNeuralHash2ONNX](https://github.com/AsuharietYgvar/AppleNeuralHash2ONNX)
