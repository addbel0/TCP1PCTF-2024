# OSINT

Here's my writeup for 5 out of 7 solved challenge.

## Bandit

>Author: **Jieyab89**
>An Jieyab as informant took a photo of a vehicle, can you find the location?

The flag is name the location and date example TCP1P{Town, Coutry. Month Year}
![The suspect](/img/suspect.jpg)
### Solution

Searching with google lens will showing a [website](https://platesmania.com/id/nomer24795105) for plate that contain the details
![[Pasted image 20241013223240.png]]

The flag `TCP1P{Malang, Indonesia. October 2019}`

## Night Live at Indonesia

> Author: **Jieyab89**
> Oeman is a nightlife maniac tourist in 2007 - 2010, he came back to Indonesia to enjoy the nightlife but the place was closed, what was the name of the place? And who was the person who closed the place?

The flag is name of Man was close the place and the birth date example TCP1P{Abdul Risna Ardana, 14 July 1669}

## Solution

Upon opening the attachment.zip, the zip contain photo from nightclub. One of the photos contain a watermark for famous nightclub at that time **STADIUM**
![[Pasted image 20241013223554.png]]

Searching up for who closed the place is easy, because it's a big news at that time. The answer will be `Basuki Tjahaja Purnama`
![[Pasted image 20241013223740.png]]

Another google times for `Basuki Tjahaja Purnama` shows his details
![[Pasted image 20241013223858.png]]
The flag `TCP1P{Basuki Tjahaja Purnama, 29 June 1966}`

## The Investigator

>Author: **Jieyab89**
>Help Jieyab found the newspaper. When was this newspaper published?

The flag name is date TCP1P{Date Month Year}

![[Pasted image 20241013224053.png]]

### Solution

Looking at the newspaper seems like a old Dutch newspaper. Looking for old netherlands newspaper index shows this [website](https://www.kb.nl/en/research-find/datasets/delpher-newspapers)
![[Pasted image 20241013224232.png]]


Opening the website will bring us to another data site
![[Pasted image 20241013224415.png]]

![[Pasted image 20241013224443.png]]

Searching the keyword `PETRUS roeit Indonesische misdaad uit` will shows one result with published date `17 December 1983`
![[Pasted image 20241013224626.png]]

The flag is `TCP1P{17 December 1983}`

## 