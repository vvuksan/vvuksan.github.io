---
author: admin
comments: true
date: 2010-01-22 13:38:19+00:00
layout: post
slug: password-complexity-madness
title: Password complexity madness
wordpress_id: 15
---

You know the pitch. Each time you create an account for a "secure" site you are forced to come up with a complex password ie. you need to have a number, a capitalized letter, perhaps a special character such as + or -. Trouble is policies differ so on one site password has to be a minimum length, maximum length, some don't allow special characters etc. The thing is at one point in time this made sense and was required to keep basic security but it may not make sense today.

Ages ago computer systems (in particular UNIX systems) used to store passwords in a hashed format (hash . You can read more on [cryptographic hashes on Wikipedia](http://en.wikipedia.org/wiki/Cryptographic_hash_function). The trouble is that these hashes were available for any user to see ie. you could copy a password file (/etc/passwd) or use YP/NIS tools to get a list of all passwords in an organization. Once you have the password file you do not know what the passwords are however you can take a word dictionary start computing hashes since a particular password will always convert to the same hash and compare it if there are any matches in your password file. If you find a match you know have "discovered" users password. This is often referred to as off-line password cracking since it allows you derive passwords without interacting with the target system. This has many advantages since you can try millions of passwords quickly and the target system's administrator will not be alerted. Based on this fact password policies were instituted that mandated password complexity since passwords with complexity ie. 9pc_miu would be nearly impossible or very hard to break (it may take years to break it). This made sense then.

However it doesn't make much sense now since on most systems regular users have no access to the password hashes. On UNIX systems "shadow" (/etc/shadow) is used to hide them or you may be using LDAP which has the capability of hiding password hashes, etc. The only users that have access to those hashes are administrator however they have other ways of acquiring your passwords. Thus your real exposures in order of importance are



	
  * Trivial passwords or easily guessable password ie. 123456, 1234, date of birth

	
  * Using same password across different sites ie. this is a problem if e.g. site A.com gets hacked and hackers are able to determine your password and log into site B.com


I actually feel that password complexity breeds poor security since people will write down complex passwords instead of remembering them. Just remember how many times have you seen passwords on post-it notes on someone's monitor. Perhaps it is time to scrap the password complexity and use something simpler.
