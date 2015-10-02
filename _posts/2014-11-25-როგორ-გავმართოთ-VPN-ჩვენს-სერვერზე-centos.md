---
layout: post
title: "როგორ გავმართოთ VPN ჩვენს სერვერზე (centos)"
author: ცოტნე ნაზარაშვილი
date: 2014-11-25 20:54:34
---
თემას ცოტა განვავრცობ და დავიწყებ იქიდან თუ რა არის VPN?

Virtual private network (VPN) არის ვირტუალური შიდა ქსელი რამდენიმე კომპიუტერს შორის, რაც საშვალებას იძლევა გამოვიყენოთ სხვადასხვა შიდა ქსელის უპირატესობები რეალური შიდა ქსელის გარეშე. ასევე საშვალებას იძლევა შევიცვალოთ IP მისამართი.

IP მისამართის შეცვლისთვის VPN გაცილებით უკეთესია ვიდრე proxy, რადგან იძლევა ბევრად უფრო ხარისხიან კავშირს ინტერნეტთან.

### როგორ დავაყენოთ VPN centos linux სერვერზე?

ამისთვის საკმარისია რამდენიმე ბრძანება გავუშვათ სერვერზე.

ეს ბრძანებები ჩამოვწერე gist-ში
<script src="https://gist.github.com/thecotne/b3950c17e3d8aedd99aa.js"></script>

სერვერზე გავუშვათ ბრძანება wget და გადმოვწეროთ ზემოთ მოცემული სკრიპტი
`wget https://gist.githubusercontent.com/thecotne/b3950c17e3d8aedd99aa/raw/vpn.sh`

nano-თი ან რამე სხვა ედიტორით შევცვალოთ vpnuser და vpnpassword ჩვენთვის სასურველი სახელით და პაროლით,
თუ არ გვაქვს nano უბრალოთ დავაყენოთ შემდეგი ბრძანებით:
`yum install nano -y`
და იმისთვის რომ გავხსნათ ჩვენი სკრიპტი nano ში გავუშვათ შემდეგი ბრძანება:
`nano vpn.sh`

შემდეგ იმისთვის რომ დავაყენოთ ჩვენი VPN გავუშვათ ჩვენი სკრიპტი შემდეგი ბრძანებით:
`sudo bash vpn.sh`

ამით VPN უკვე დაყენებული იქნება და შეგვიძლია გავტესტოთ.
თუ არ იცით როგორ უნდა VPN თან დაკავშირება თქვენი OS იდან წაიკითხეთ ქვემოთ მოცემული სტატიებიდან თქვენი OS-ის შესაბამისი:

* [windows 7 და 8](http://www.howtogeek.com/134046/how-to-connect-to-a-vpn-in-windows/)
* [OS X Yosemite](http://support.apple.com/kb/PH18563)
* [OS X Mavericks](http://support.apple.com/kb/PH14079)
* [OS X Mountain Lion](http://support.apple.com/kb/PH11067)
* [Ubuntu](https://wiki.ubuntu.com/VPN)
* [IOS](http://support.apple.com/en-is/HT201550)
* [Android](http://www.howtogeek.com/135036/how-to-connect-to-a-vpn-on-android/)
* [Windows Phone](http://www.windowsphone.com/en-gb/how-to/wp8/connectivity/use-a-vpn-connection)

გმადლობთ ყურადრებისთვის, იმედი მაქვს ჩემი პოსტი ვინმეს გამოადგება )
