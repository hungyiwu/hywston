# Kali linux penetration testing: Virtualbox or Docker?

## Background
Recently I've been into cyber security and been playing in TryHackMe ([link](https://tryhackme.com)). I noticed many penetration test (pentest) tutorials use Virtualbox for Kali linux, while only limited ones use Docker.

Why?

After digging around on the internet and some deep thoughts, here's how I'd describe the difference:

## What it's like using Virtualbox:
1. Spawn one virtual machine (VM) running the web app to be tested (ex. OWASP Juice Shop)
2. Spawn another VM running Kali linux
3. In Kali linux, launch Burp Suite, Firefox + foxy proxy, and a terminal for things like `nmap`
4. Now you're like pentesting a **web server running a web app** from **another machine running Kali linux with a full suite of pentest tools**

## What it's like doing the same thing using Docker:
1. Spawn one container running the web app to be tested (ex. OWASP Juice Shop)
2. Spawn another container running Burp Suite (in Kali linux environment)
3. Spawn another container running Firefox + foxy proxy (in Kali linux environment)
4. Spawn another container running bash for things like `nmap` (in Kali linux environment)
5. Now you're like pentesting a **web app** using tools like Burp Suite, Foxy proxy, `nmap` from **the same machine** alltogether

## Discussion
Using an analogy of AOE II (Age of Empire II, a real-time strategy war game), pentesting using Virtualbox is like playing as two players. You build two different countries, collecting resources separately. One builds a facility A to generate combat units A1; another builds another facility B to generate combat units B1. Then you have the two countries fight against each other using different units A1 & B1.

Pentesting with Docker is like playing as one single player, ie. yourself. You build one country, collect resources, then build two facilities A and B to generate different combat units A1 and B1. You then use A1 to attack B1 like they are enemies because, you know, normal human society behavior.

## (Personal and totally non-professional) Guideline to choose between Virtualbox and Docker when learning Kali linux pentesting in this use case
* If you just want to test how **unit** A1 and B1 fight against each other, Docker will get you there faster.
* If you want to learn & test full invasion tactics against **a country**, you really want Virtualbox because that's closer to a full invasion simulation.
