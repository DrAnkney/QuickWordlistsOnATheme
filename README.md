# Building a Quick Wordlist on a Theme
Building a quick password cracking wordlist when you have a hint for a topic but not a lot of time


If you know something about your target, then you can build a wordlist that might be more likely to be successful than a giant list that has nothing to do with them and/or their interests. But this is an issue for a password cracker such as hashcat or john the ripper (less so perhaps for aircrack-ng), and they will fuss at you if your wordlist is too short. Anyway, your person might have very specific interests, a particular address, birthday, etc but still do some simple obfuscating, from Fifi0527 to F1f10527!, for example.

**But the main reason for this post is to help you build a quick list if, say, you're in a CTF and have been given a clue to a theme for your list but don't have days and months to build  out a really great list.**

So here's a method to build a *super quick* list that's *long enough* so hashcat won't yell at you; try it with your own theme; my example is fruit-related.

1. Find a nice website with relevant words. I suggest (after some trial but mostly error) 
	a. searching with the word "list" (so "fruit list" rather than just "fruit") and 
	b. leaning towards something encyclopedic (such as wikipedia rather than a schoolteacher's vocabulary list), and
	c. keeping it simple (so simple.wikipedia as opposed to en.wikipedia, for example).
2. Scrape that website using cewl. If you think that the links will lead to more relevant words (for example, different names for the same fruit), then use a depth of 1. There might be no need to go deeper than that; it will add words to your list, but they may be irrelevant, and we want to add length that's more than just fluff in step 3.

```
cewl -v -d 1 -w fruit100.txt https://simple.wikipedia.org/wiki/List_of_fruits 
```
(I suggest verbose on this because otherwise you could sit there forever  thinking cewl wasn't working, because this can take a minute if $d>1$ or if your website is more involved.) This command with this website gave me 21,584 words in a few minutes.

3. Now use some rules to catch things such as different capitalization, or switching out an a for a 4 or @, or adding a number to the end of the word. Remember that we want a long list here with a minimum of time expended, pick a few of the built-in hashcat rules lists that aren't too long. The d3ad0ne.rule ruleset, for example, will take you a *while* to run, even more so with something like OneRuleToRuleThemAll.rule. So maybe just a little best-of rule or two. Run them on your list separately, into separate files to make things super fast.

```
hashcat --force fruit100.txt -r /usr/share/hashcat/rules/best64.rule --stdout > fruit1.txt

hashcat --force fruit100.txt -r /usr/share/hashcat/rules/generated2.rule -- stdout > fruit2.txt
```

You can even make up your own set of rules and run it on the list:

```
hashcat --force fruit100.txt -r rules.rule --stdout > fruit3.txt
```

The rules.rule file is one I made from the excellent tutorial on rules lists at https://www.4armed.com/blog/hashcat-rule-based-attack/, who also explains how to keep track of how well your rules work for you as you use them over time. Check it out. 

Here's my rules.rule, based off his suggestions:
```
#do nothing
:
#Lowercase
l
#Uppercase
u
#Capitalize first character
c
#Add '1' to the end
$1
#Add '2' to the end
$2
#Add '3' to the end
$3
#Add '4' to the end
$4
#Add '5' to the end
$5
#Add '6' to the end
$6
#Add '7' to the end
$7
#Add '8' to the end
$8
#Add '9' to the end
$9
#Add '123' to the end
$1 $2 $3
#Substitute 'a' with '@'
sa@
#Substitute 'a' with '4'
sa4
#Substitute 'e' with '3'
se3
#substitute 'l' with '1'
sl1
#Substitute 'a' with '@', 'e' with '3', 'l' with '1'
sa@ se3 sl1
#Substitute 'a' with '4', 'e' with '3', 'l' with '1'
sa4 se3 sl1
#Add the word 'crazy4' to the beginning
^4 ^y ^z ^a ^r ^c
```

4. Now put your tricked out lists together:

```
cat fruit1.txt fruit2.txt fruit3.txt >> crazy4fruit.txt
```
If you have any passwords that YOU would use if you had a password on this theme, then tack those on the end.
```
echo "crazy4fruit" >> crazy4fruit.txt
echo "bananas4fruit" >> crazy4fruit.txt
```

5. Find out if your wordlist is long enough for you:
```
cat crazy4fruit.txt | wc -l
```
Our wordlist crazy4fruit.txt now has 130,015 words, up from about 21,500 after the web scrape with cewl and enough to crack what I needed to crack with it. Happy cracking!



