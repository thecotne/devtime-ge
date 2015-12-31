---
layout: post
title: "AppDomains via C#"
author: თამაზ ბაღდავაძე
date: 2015-12-29 00:37:00
---
ამ სტატიაში განვიხილავთ AppDomain-ების შექმნას და გამოყენებას .net-ში,
და ასევე პლაგინების შექმნის მარტივ მაგალითს.

დასაწყისისთვის განვიხილოთ პრობლემა, რომლის გამოც შეიქმნა AppDomain-ები.

თანამედროვე ოპერაციულ სისტემაში ნებისმიერი პროგრამა ეშვება პროცესში.
პროცესი ეწოდება გაშვებულ პროგრამას, მის მიერ გახსნილ ფაილებს, ნაკადებს და ა.შ.

ყველა პროცესს აქვს თავისი მისამართების სივრცე ([address space](https://en.wikipedia.org/wiki/Address_space)),
რომლის ზომაც 32 ბიტიან ოპერაციულ სისტემაში არის 4გბ, ხოლო 64 ბიტიან სისტემაში - 16გბ.

რადგანც ყველა პროცესს თავისი მისამართების სივრცე აქვს,
მათ არ შეუძლიათ პირდაპირ მიმართონ ერთმანეთის მეხსიერებას
(რადგან ძირითადად ყველა პროგრამა ეშვება
[user mode](https://en.wikibooks.org/wiki/Windows_Programming/User_Mode_vs_Kernel_Mode)-ში).
მაგრამ ამავე დროს მათ ხშირად
სჭირდებათ ინფორმაციის გაცვლა. ამისათვის გამოიყენება შემდეგი პროცესთა შორი კომუნიკაციის
([IPC](https://en.wikipedia.org/wiki/Inter-process_communication))
ტექნოლოგიები :

  1. Socket
  2. Pipe
  3. LPC (Local Procedure Call)
  4. RPC (Remote Procedure Call)
  5. DDE (Dynamic Data Exchange)
  6. COM (Component Object Model)

და ბევრი სხვა ტექნოლოგია.

ყველა ამ ტექნოლოგიას აქვს დიდი ნაკლი. ინფორმაციის გაცვლა ხდება ოპერაციულ სისტემის გავლით.
ზოგჯერ ოპერაციულ სისტემის ბირთვის გავლით, უარეს შემთხვევაში კი ქსელით.
ნებისმიერი ასეთი ინფორმაციის გაცვლა არის ძალიან ნელი, შედარებით იმ ოპერაციებთან რაც
ხდება პროცესის შიგნით.

ამის გამო .net-ში არსებობს კლასი - AppDomain, რომელიც უზრუნველყოფს პროცესის შიგნით გამოყოფილი
მეხსიერების შექმნას.

AppDomain არის იზოლაციის მინიმალური ერთეული CLR-ისთვის. ის იქმნება პროცესის შიგნით,
და ოპერაციულმა სისტემა არ იცი მის შესახებ. AppDomain იქმნება მხოლოდ და მხოლოდ managed code-ში
დაწერილ აპლიკაციებში. ნებისმიერ აპლიკაციას აქვს 1 მთავარი AppDomain, რომლიდანაც იწყება პროგრამის
შესრულება. ნებისმიერი ტიპი და DLL პროგრამაში ჩატვირთვისას მიეკუთვნება ერთ-ერთ AppDomain-ს.
არის მხოლოდ 1 გამონაკლისი - mscorlib.dll, რომელიც არის domain-neutral, ანუ საზიარო ყველა AppDomain-ისთვის.
მსგავსი DLL-ებისთვის CLR-ს აქვს სპეციალური ჰიპი (heap), რომელში არსებული Type Objeсt-ები საზიაროა ყველა
AppDomain-ისთვის.

ქვემოთ მოცემულ სურათზე წარმოდგენილია ვინდოსის პროცესი და 2 AppDomain

![IMG](http://i.imgur.com/epRl3Mj.png)

AppDomain - ებს შორის ინფორმაციის გაცვლისთვის გამოიყენება ტექნოლოგია - .Net Remoting.
მოცემული ტექნოლოგია არის გაცილებით სწრაფი ვიდრე IPC, რადგან ყველაფერი ხდება პროცესის შიგნით,
და ასევე არის უსაფრთხო(რასაც განაპირობებს CLR).

ასე რომ AppDomain გვაძლევს საშუალებას უსაფრთხოდ გამოვიყენოთ სხვისი კოდი ჩვენს პროგრამაში.
მაგალითად შევქმნათ მეორე AppDomain და მასში ჩავტვირთოთ უცხო კოდი,
მივცეთ შეზღუდული უფლებები და გამოვიყენოთ.

ასევე AppDomain - ებს აქვთ კიდევ ერთ პლიუსი. როგორც ვიცით .net-ში ტიპები, უფრო
ზუსტად ეგრეთწოდებული Type object-ები იშლება მხოლოდ მაშინ როდესაც აპლიკაცია დაასრულებს
მუშაობას. ასევე თუ ჩავტვირთავს რაიმე DLL-ს, მისი წაშლა შეუძლებელია. მაგრამ თუ DLL-ს ჩავტვირთავს
AppDomain-ში, მისი წაშლა შეგვიძლია. მაგრამ აღსანიშნავია რომ ამისთვის უნდა წავშალოთ მთლიანად
AppDomain, რის შედეგადაც წაიშლება მასში არსებული ყველა ტიპი და DLL.

ახლა კი რაც შეეხება უსაფრთხოებას. თუკი რომელიმე AppDomain-ში მოხდება შეცდომა, ეს არანაირად არ
აისახება სხვა AppDomain-ებზე, რადგანაც თითოეული AppDomain-ისთვის იქმნება გამოყოფილი მეხსიერება,
მუშაობს გამოყოფილი Garbage Collector, უსაფრთხოების წესები და
ასევე იქმნება ცალკე სტატიკური ცვლადები, რაც ნიშნავს რომ თუ ორ სხვადასხვა AppDomain-ში ჩავტვირთავთ
ერთი და იგივე კლასს რომელსაც აქვს სტატიკური წევრები, შეიქმნება ორი სხვადასხვა Type Object.

ახლა განვიხილოთ მარტივი მაგალითი

```c#

using System;
using System.Runtime.Remoting;

namespace Domains
{
	//[Serializable]
	class PersonTool : MarshalByRefObject
	{
		public Person ChangeName(Person s, string newUserName)
		{
			Console.WriteLine("ChangeUserName, called from domain :  {0}",
				AppDomain.CurrentDomain.FriendlyName);

			s.Name = newUserName;
			return s;
		}
	}

	[Serializable]
	class Person  //: MarshalByRefObject
	{
		public string Name;
		public override string ToString()
		{
			return $"Name : {Name}, hash : {GetHashCode()}";
		}
	}

	class Program
	{
		static void Main()
		{
			AppDomain secondDomain = AppDomain.CreateDomain("Second Domain");

			ObjectHandle userHandle = secondDomain.CreateInstance("Domains", "Domains.PersonTool");
			var userProxy = (PersonTool)userHandle.Unwrap();

			Console.WriteLine( RemotingServices.IsTransparentProxy(userProxy)
								  ? "is proxy"
								  : "is not proxy");

			var user = new Person() {Name = "tazo"};
			var newUser =  userProxy.ChangeName(user, "tazo 2.0");

			Console.WriteLine(user);
			Console.WriteLine(newUser);

			Console.WriteLine( "are same objects : " +  ReferenceEquals(user, newUser));

			Console.ReadKey();
		}
	}
}

```
როგორც ვხედავთ AppDomain-ის შექმნა ხდება AppDomain კლასსზე სტატიკური მეთოდის CreateDomain - ის
გამოძახებით. შემდეგ ხაზზე ვქმნით PersonTool კლასის ობიექტს ჩვენს მიერ შექმნილ AppDomain-ში. მეთოდი
CreateInstance გვიბრუნებს ObjectHandle კლასის ეგზემპლიარს, რომლიდანაც შემდეგ ვიღებთ PersonTool-ის ობიექტს
მეორე AppDomain-დან. ერთი შეხედვით, ცვლადი userProxy, არის PersonTool-ის ტიპის, მაგრამ სინამდვილეში
ის არის ერთგვარი proxy object,  რომელიც დიზაინ პატერნ proxy - ის საშუალებით გვაძლევს საშუალებას ვიმუშაოთ
მეორე AppDomain-ში შექმნილ PersonTool-ის ობიექტთან. თავად კლასი PersonTool არის შვილობილი კლასის
MarshalByRefObject. ეს კლასი უმატებს PersonTool-ს ფუნქციონალს რომლითაც შეგვიძლია მასზე სხვა დომენში
მანიპულირება. შემდეგ ხაზზე ვამოწმებთ არის თუ არა დაბრუნებული ობიექტი proxy. თუ PersonTool არ იქნება
MarshalByRefObject-ის შვილობილი(და ექნება ატრიბუტი serializable), დავინახავთ მესიჯს - `is not proxy`, რაც იმას ნიშნავს რომ ობიექტი შეიქმნება
პირველ AppDomain-ში და არა მეორეში.

შემდეგ ვქმნით User კლასის ობიექტს და გადავცემთ პროქსის საშუალებით მეთოდს ChangeName, რომელიც დააბრუნებს
User კლასის ობიექტს. როგორც ვხედავთ User კლასი აქვს Serializable ატრიბუტი, რაც იმას ნიშნავს
რომ იგი სერიალიზდება და ისე გადაეცემა მეთოდს. მეთოდში იგი დესერიალიზდება, ეცვლება წევრი - Name,
შემდეგ ისევ სერიალიზდება და ბრუნდება. ასე რომ ამ კოდის გაშვების შემდეგ user-ს და newUser-ს ექნებათ
განსხვავებული ჰეშკოდები. ასევე newUser - ს ექნება ახალი სახელი.

ახლა User კლასი გავხადოთ შვილობილი კლასისა MarshalByRefObject და ისევ გავუშვათ. ამ შემთხვევაში
სერიალიზაციის ნაცვლად მეორე AppDomain-ს გადაეცემა რეფერენსი პირველ AppDomain-ში შექმნილ ობიექტზე.
დავინახავთ რომ ეს ერთი და იგივე ობიექტებია `are same objects : true`.


AppDomain - ის წაშლა ხდება სტატიკური მეთოდით AppDomain.Unload.
მაგალითი :

```c#
using System;

namespace Domains
{
	class Program
	{
		static void Main()
		{
			AppDomain domain = AppDomain.CreateDomain("MyDomain");

			Console.WriteLine("domain name : " + domain.FriendlyName);

			AppDomain.Unload(domain);

			try
			{
				Console.WriteLine("domain name: " + domain.FriendlyName);
			}
			catch (AppDomainUnloadedException e)
			{
				Console.WriteLine(e.GetType().FullName);
				Console.WriteLine("MyDomain does not exist.");
			}

			Console.ReadKey();
		}
	}
}

```

ახლა განვიხილოთ შემდეგი მაგალითი. visual studio-ში შევმნაქთ ახალი solution, შევქმნათ 3 პროექტი.
პირველში ჩავწეროთ მხოლოდ შემდეგ კოდი:

```c#
namespace App1
{
public static class Program
{
	public static void Main()
	{
		System.Console.WriteLine("project 1  ", AppDomain.CurrentDomain.FriendlyName);
	}
}
}
```

მეორეში ჩავწეროთ :

```c#
namespace App2
{
 public static class Program
 {
	 public static void Main()
	 {
		 System.Console.WriteLine("project 2  ", AppDomain.CurrentDomain.FriendlyName);
	 }
 }
}
```

მესამეში კი :

```c#
namespace MultyDomainApp
{
	static class Program
	{
		static void Main()
		{
			AppDomain domain1 = AppDomain.CreateDomain("Domain 1");
			AppDomain domain2 = AppDomain.CreateDomain("Domain 2");

			domain1.ExecuteAssembly("App1.exe");
			domain2.ExecuteAssembly("App2.exe");

			System.Console.ReadKey();
		}
	}
}
```

დავაკომპილიროთ, გავუშვათ მესამე პროექტი და ვნახავთ რომ გაეშვება პირველი ორი პროექტი. აღსანისნავია რომ
გვაქვს მხოლოდ 1 ნაკადი, რომელიც მუშაობს სამივე AppDomain-თან.

AppDomain-ებზე ჯერჯერობით სულ ეს იყო. პლაგინების შექმნის მაგალითს კი შემდეგ პოსტში შემოგთავაზებთ.
