---
title: "Sealgair's Oceania ICC Qualifier Challanges 2022"
date: 2022-04-24
excerpt_separator: "<!--more-->"
categories:
  - Write Up
tags:
  - CTF
  - Write Up
  - Cyber Security
  - Projects
---

Since the beginning of this year I have had the pleasure of being involved in the [Oceania ICC Qualifiers 2022](https://austiccquals.cyber.uq.edu.au/). In mid-March, I thought up and planned out some challenges, testing concepts for two weeks. From the beginning of April, I began working on the development of three challenges outright with another in the back of my mind. 

I will go through some of the thought processes and some of my own challenges faced making these. My goals in developing my challenges were to test overall critical thinking skills and ability to solve problems with imperfect information.

## tea_pain
The `tea_pain` challenge was one of my first conceptualised challenges. It is based off the image below.

![tea_pain](/sealgair/assets/images/OICCQ_Post/tea_pain.png)

This image was converted using [Text-Image](https://www.text-image.com/) to be made up of the Arabic numerals. This conversion can be seen as a coordinate system on the Cartesian Plane.  From that I created the following generator to create the keys the competitors needed to find. 

```python
import random as r

# The desired number of coordinates and x,y size of array
number_of_coordinates = 100
x_length = 199
y_length = 399

new_array = []
chosen_number = []
x_coord = []
y_coord = []
output = []

with open("array.txt") as f:
    array = [row.split() for row in f]

temp = 0
for row in array:
    for col in row:
        temp = [int(x) for x in str(col)]
        new_array.append(temp)

for i in range(number_of_coordinates):
    x = r.randint(0, 199)
    y = r.randint(0, 399)
    x_coord.append(x)
    y_coord.append(y)
    chosen_number.append(new_array[x][y])

print("Chosen Numbers")
print(chosen_number)

print("------------------------------------------")

for i in range(number_of_coordinates):
    output.append((x_coord[i], y_coord[i]))

print("Coordinates")
print(output)
```

Next, competitors were to use an un-altered version of the `tea_pain` image and [Steghide](http://steghide.sourceforge.net/) to uncover a second set of coordinates and a `.pdf` encrypted with the key that came from those coordinates. Unfortunately the unaltered image was not large enough to hold the `.txt` file containing the coordinates, let alone an encrypted `.pdf`. I used another image that was larger in size and appeared to work.

There was an bug, however, with Steghide that would only allow one file to be hidden within the image at a time. Each time I would add a second file, it would corrupt the other. This should have been found during testing, but I only tested that the file I embedded was hidden directly after adding it, missing that problem.

![tea_pain](/sealgair/assets/images/OICCQ_Post/OICC2.PNG)

## Shuffled
This concept came from a disagreement where someone said that you could shuffle the letters in the alphabet and so long as you know what the key is, no one else could figure it out. 

To make this I took a UTF-8 file from [this repository](https://github.com/bits/UTF-8-Unicode-Test-Documents) and shuffled the characters in one of the files. I then iterated over the the decided flag and used Python's `.find()` function to find the position of all relevant alphanumeric characters. I then found the greatest common denominator between all the character positions and used that to make keys to the flag.

I didn't save the code that I used to develop this unfortunately, I will ensure I do in the future. If I were to make a similar challenge in the future, I would aim to make it a little more complex. The final challenge, the competitors only needed to find four integers that get the flag. This can be done through brute-forcing or realising that `oiccflag{...}` would be in the list. The second option takes longer and hold no benefit to the player for using their head. 

Overall, I proved my point that shuffling characters randomly doesn't make for good encryption.

![tea_pain](/sealgair/assets/images/OICCQ_Post/OICC3.PNG)


## Computer Engineering Final Exam
April has been a consistently busy month for me. Because of this, I found it hard to justify to myself making a difficult challenge while I need to study. After doing one of my weekly quizzes, I realised that I *could* do both. 

The idea of this was to make the code difficult to read and the contents inside appear too difficult to complete for those rushing through the challenges. The final product was much simpler than what I had originally envisioned, but it still was not easy. 

There were two key components the competitors needed to pay attention to. 
1. `pick_a_card` which set what questions would be asked. 
2. `RTH`, `ID`, and `current` whose values were used for later questions.

Competitors needed to solve five questions total and then two *are you human* questions. Of the five testing questions, only question four did not have the solution in the script the players referred to.

Question 1 let the competitors know what was chosen by `pick_a_card` by providing a simple question they needed to respond to.

Question 2 provided the `RTH`, `ID`, and `current` values to the player that was needed for all other questions. The solution to the question could be resolved for any case by using the code provided,
```python
IN = VTH/RTH
ID = 1/(RTH) * voltage - IN
voltage_real = np.linspace(0, VTH, 50)
diode = np.linspace(0, 100, 50) ** 2.5
Vd = np.argwhere(np.diff(np.sign(voltage_real - diode))).flatten()

operating_point = "0, 0"

try:
    operating_point = str(int(Vd[1])) + ", " + str(int(voltage_real[Vd[1]]))
except:
    operating_point = "NULL"
```
If a operating point existed for the diode, the players could get that, otherwise they would provide `NULL`. They didn't require any mathematical, electrical engineering, or numpy knowledge to solve this, just critical reasoning. In fact, the solution is overly simplified and the answer is not quite correct to the analysis required for finding the operating point of a diode.

Question 3 was originally going to present only the values of the components and the player had to infer which question was being asked from `pick_a_card`. The questions given all revolved around passive filters. The values found were to affect later questions, but the complexity of the math is not what was being tested. This ended up being just a comprehension question and another point to ensure the competitors don't get thrown off by unfamiliar questioning.

Question 4 asked players to find the shortest path from `Vin` to `Vout` of a circuit when all they are presented with was a Python dictionary. This dictionary was then shuffled around 9 times to create 10 visual permutations of the same graph. The goal of this was to get the players to once again critically think. Creating graph algorithms is not the goal of the cybersecurity challenge. If they were to hand-draw the graph, they would see that it would always follow the variable pattern `a + b + c + d + e + f = Vout = shortest_path`. During the competition itself, this challenge did cause the most problems for players. 

Question 5 had players find the voltage drop of the circuit using the `shortest_path` found before, the `current` given in Question 2 and the `trace_material` selected by `pick_a_card`. This had originally had more modifiers to it, including a `plus_minus_percent` value that would change the `trace_material` conductivity to be more 'realistic' but it was decided against. 

### What was cut
I had created a Boolean statement generator using Python's `eval()` function. Unfortunately with the randomisation of brackets, the statements were almost always false. I had considered making a simple case statement system for it, but I had already made the introductory questionnaire for player's to know what was chosen by `pick_a_card`.

What was chosen by `pick_a_card` was going to have additional `if then` modifiers such as `if pick_a_card % 3 == 0:` but that was getting to be a bit much for this challenge. 

As stated the values found in Question 3 were going to modify other aspects of the questions including a modifier on the Question 4's `shortest_path` this was again decided against because mathematical complexity was not being tested in this challenge. 

![tea_pain](/sealgair/assets/images/OICCQ_Post/OICC1.jpg)

## Reflection
One of the biggest things for me to do better in the future would be to start much earlier and test my challenges all the way through to the end. I also want to look into better planning methods than random writing down of ideas and testing concepts only. 

Also I didn't realise how little I actually understood Docker. I have used it with guides/course-work throughout the year but I haven't set my own up before. It become very obvious I didn't understand exactly how it works from the Wednesday of the week leading to the competition. I will spend some time making more Docker containers and understanding the technology properly.

### Competitor Feedback
- Good introduction to using `pwntools`
- A lot of non-deterministic but easily scriptable questions.
- Provide an example of what the inputs *should* look like
- Provide context for more abstract bits. Especially regarding Electrical Engineering Challenges' Question 4.
- Too much reliance on obfuscation and misdirection.
