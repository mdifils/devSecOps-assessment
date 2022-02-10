# devSecOps-assessment

## Steps to find the culprit

1. Reading the instructions and moving to mystery directory  

>`$ cat instructions`  
>`$ cd mystery`

2. Playing around with `head <filename>` to have an idea of each file's content

3. Collecting all the clues at the crime scene

```shell
$ cat crimescene | grep CLUE
CLUE: Footage from an ATM security camera is blurry but shows that the perpetrator is a tall male, at least 6'.
CLUE: Found a wallet believed to belong to the killer: no ID, just loose change, and membership cards for AAA, Delta SkyMiles, the local library, and the Museum of Bash History. The cards are totally untraceable and have no name, for some reason.
CLUE: Questioned the barista at the local coffee shop. He said a woman left right before they heard the shots. The name on her latte was Annabel, she had blond spiky hair and a New Zealand accent.
```
4. Tracking down the witness by the name `Annabel`
First have a look to people file then search for Annabel address.

```shell
$ head people
***************
To go to the street someone lives on, use the file
for that street name in the 'streets' subdirectory.
To knock on their door and investigate, read the line number
they live on from the file.  If a line looks like gibberish, you're at the wrong house.
***************

NAME    GENDER  AGE     ADDRESS
Alicia Fuentes  F       48      Walton Street, line 433
Jo-Ting Losev   F       46      Hemenway Street, line 390

$ grep "Annabel" people
Annabel Sun     F       26      Hart Place, line 40
Oluwasegun Annabel      M       37      Mattapan Street, line 173
Annabel Church  F       38      Buckingham Place, line 179       
Annabel Fuglsang        M       40      Haley Street, line 176
```
5. Finding out which of the two women is the witness by reading the correct line from the streets they live on

First have a look into streets folder then search the file corresponding to Annabel's address.

```shell
$ ls streets | head -n 3
Abbot_Street
Acton_Street
Addington_Road

$ head -n 40 streets/Hart_Place | tail -n 1
SEE INTERVIEW #47246024

$ head -n 179 streets/Buckingham_Place | tail -n 1
SEE INTERVIEW #699607
```

Now have a look into interviews folder and look for the interview files corresponding to the numbers we found

```shell
$ cat interviews/interview-47246024
Ms. Sun has brown hair and is not from New Zealand.  Not the witness from the cafe.

$ cat interviews/interview-699607
Interviewed Ms. Church at 2:04 pm.  Witness stated that she did not see anyone she could identify as the shooter, that she ran away as soon as the shots were fired.

However, she reports seeing the car that fled the scene.  Describes it as a blue Honda, with a license plate that starts with "L337" and ends with "9"
```

The witness is **Annabel Church** and the culprit has a blue Honda, with a license plate that starts with "L337" and ends with "9".

6. Chek people who own a blue Honda

First have a look to vehicles file and then find blue Honda owners who are at least 6' tall

```shell
$ head vehicles 
***************
Vehicle and owner information from the Terminal City Department of Motor Vehicles
***************

License Plate T3YUHF6
Make: Toyota
Color: Yellow
Owner: Jianbo Megannem
Height: 5'6"
Weight: 246 lbs

$ grep -A 5 "L337" vehicles | grep -B 2 -A 3 "Blue" | grep -B 1 -A 4 "Honda" | grep -B 4 -A 1 "6'"
License Plate L337QE9
Make: Honda
Color: Blue
Owner: Erika Owens
Height: 6'5"
Weight: 220 lbs
--
License Plate L337DV9
Make: Honda
Color: Blue
Owner: Joe Germuska
Height: 6'2"
Weight: 164 lbs
--
License Plate L3375A9
Make: Honda
Color: Blue
Owner: Jeremy Bowers
Height: 6'1"
Weight: 204 lbs
--
License Plate L337WR9
Make: Honda
Color: Blue
Owner: Jacqui Maher
Height: 6'2"
Weight: 130 lbs
```

We have 4 candidates: Erika Owens, Joe Germuska, Jeremy Bowers and Jacqui Maher

7. Check membership for AAA, Delta SkyMiles and Museum of Bash History.

First have a look into memberships folder and AAA membership

```shell
$ ls memberships/
AAA  AAdvantage  Costco  Delta_SkyMiles  Fitness_Galaxy  Museum_of_Bash_History  REI  Rotary_Club  TCSU_Alumni_Association  Terminal_City_Library  United_MileagePlus

$ grep "Erika Owens" memberships/AAA

$ grep "Joe Germuska" memberships/AAA
Joe Germuska

$ grep "Jeremy Bowers" memberships/AAA
Jeremy Bowers

$ grep "Jacqui Maher" memberships/AAA
Jacqui Maher
```

Erika Owens is not the culprit. Check Delta SkyMiles membership

```shell
$ grep "Joe Germuska" memberships/Delta_SkyMiles

$ grep "Jacqui Maher" memberships/Delta_SkyMiles
Jacqui Maher

$ grep "Jeremy Bowers" memberships/Delta_SkyMiles
Jeremy Bowers
```

Joe Germuska is not the culprit. Check Museum of Bash History membership

```shell
$ grep "Jeremy Bowers" memberships/Museum_of_Bash_History
Jeremy Bowers

$ grep "Jacqui Maher" memberships/Museum_of_Bash_History
Jacqui Maher
```

8. follow instructions in solution file to find out the culprit.

```shell
$ echo "Jeremy Bowers" | $(command -v md5 || command -v md5sum)
32cacbec4b918e2fd130fd68cf818ae5 *-

$ grep -qi "32cacbec4b918e2fd130fd68cf818ae5 *-" encoded && echo CORRECT\! GREAT WORK, GUMSHOE. || echo SORRY, TRY AGAIN.
CORRECT! GREAT WORK, GUMSHOE.

$ echo "Jacqui Maher" | $(command -v md5 || command -v md5sum)
b11e474a98174cf48e185f396abe8cae *-

$ grep -qi "b11e474a98174cf48e185f396abe8cae *-" encoded && echo CORRECT\! GREAT WORK, GUMSHOE. || echo SORRY, TRY AGAIN.
SORRY, TRY AGAIN.
```

CONCLUSION: **Jeremy Bowers** is the culprit.