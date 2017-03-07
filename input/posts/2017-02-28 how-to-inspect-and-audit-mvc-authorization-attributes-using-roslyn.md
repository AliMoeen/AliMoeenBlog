Title: How to Inspect and Audit MVC Role Based Authorization Attributes Using Roslyn
Lead: We are using Roslyn instead of Reflection this time.

Date: 2/28/2017

Tags:

- MVC
- Security
- Roslyn

---

In my [previous post](how-to-inspect-and-audit-asp.net-mvc-declarative-role-based-authorization-attributes), I explained how I used Reflection to extract a text report that helps with inspecting and auditing ASP.NET MVC declarative role-based authorization attributes.

While Reflection does the job, the [.NET Compiler Platform ("Roslyn")](https://github.com/dotnet/roslyn ".NET Compiler Platform") provides a slick API to go through a Visual Studio solution, find MVC sites it it, then drill through all of the controllers defined there. Roselyn API provides a build/compile time analysis power while Reflection API provides after-compilation perspective. There is no question that Roselyn provides more power for what I intended to here. The only minor challenge is that at the time of writing this post, I found Roslyn is not as well documented and discussed as Reflection. A quick StackOverflow search provides below rough quantification. 

| SO Tag(s)        | # of Questions |
|-|-:|
| [reflection] [c#] | 8,165|
| [roslyn] | 1,381|

`*` _Numbers are as of March 1st, 2017_That is expected since Roslyn is much younger technology.

What I like about Roslyn is it can just open any solution and extract (nail-down) such rich information (everything you wish to know) from its code. I found this Roslyn approach is more testable since you don’t need to compile the application to extract what you need. It analyses the code, not assembly. 
Below is a simple command line application that opens a solution, and extracts the MVC controller types’ authorization attribute report data from its MVC Web Application Project. 
The core logic of the code is based on the below Roslyn API calls. IMO, it does not get any ~~sweeter~~ easier.

```cs
var controller = compilation.GetTypeByMetadataName("System.Web.Mvc.Controller");
…
IEnumerable<INamedTypeSymbol> symbols = SymbolFinder.FindDerivedClassesAsync(controller, solution).Result;
…
ImmutableArray<AttributeData> attribs = symbol.GetAttributes();
```

Here is a working code sample:

<script src="https://gist.github.com/AliMoeen/21416e979659136f260f0f0a970045ef.js"></script>

Above code dumps a text report to stdout Console. A captured console output is like [this](files/roslyn-mvc-authorization-attributes-report-sample.txt).  

## Ideas and Possible Next Step

The ideas I explained in my [previous](how-to-inspect-and-audit-asp.net-mvc-declarative-role-based-authorization-attributes#ideas) post apply here as well.

Here are two more:

+ **[Roslyn Visual Studio Code Analyzer](https://github.com/dotnet/roslyn/wiki/How-To-Write-a-C%23-Analyzer-and-Code-Fix)**: should be the ultimate impelementation target for any code analysis and reporting usecase like what I discussed here.
 
+ **LinqPad**: Another appealing idea is to do the Roslyn-based queries and reporting in [LinqPad](https://www.linqpad.net/). Hmmm ... it could be an idea for one of my next posts.

# Conclusion
In my recent two posts, I discussed the need for analyzing an application's source code and extract reports based on certain conditions. 
The particuar use case I explained here is to inspect and make sure that there is no security sensitive MVC Controller Action that supposed to be secured left un-authorized in an MVC application. There are more use cases for this type of reports. For example, a developer team might be interested in a report that extracts Unnamed Numerical Constants https://en.wikipedia.org/wiki/Magic_number_(programming)#Unnamed_numerical_constants  

I am going to take the advantage of Roslyn in my future projects. 

