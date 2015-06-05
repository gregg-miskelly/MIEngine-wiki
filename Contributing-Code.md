###Contributing Code

Before submitting a feature or substantial code contribution please discuss it with the team. The team rigorously reviews and tests all code submissions. The submissions must meet an extremely high bar for quality and design. 

The MIEngine project follows a similar developer guide to [.NET](https://github.com/dotnet/corefx/wiki/Developer-Guide).  The team enforces this by regularly running the [.NET code formatter tool](https://github.com/dotnet/codeformatter) on the code base.  Contributors should ensure they follow these guidelines when making submissions.  

For now, the team has set the following limits on pull requests:

- Contributions beyond the level of a bug fix must be discussed with the team first, or they will likely be declined. As our process matures and our experience grows, the team expects to take larger contributions.
- Only contributions against the master branch will be accepted. Authors submitting pull requests that target experimental feature branches or release branches will likely be asked target their pull request at the master branch.
- Pull requests that do not merge easily with the tip of the master branch will be declined. The author will be asked to merge with tip and update the pull request.
- Submissions must meet functional and performance expectations, including scenarios for which the team doesn’t yet have open source tests. This means you may be asked to fix and resubmit your pull request against a new open test case if it fails one of these tests.
- Submissions must follow the [.Net Foundation Coding Guidelines](https://github.com/dotnet/corefx/wiki/Coding-style)
- Contributors must sign the [Microsoft CLA](https://cla.microsoft.com/)


When you are ready to proceed with making a change, get set up to [[build|Building Testing and Debugging]] the code and familiarize yourself with our workflow and our coding conventions.

You must sign a Contributor License Agreement (CLA) before submitting your pull request. To complete the CLA, submit a request via the form and electronically sign the CLA when you receive the email containing the link to the document. You need to complete the CLA only once to cover all Microsoft Open Technologies OSS projects.

###Developer Workflow

1. Work item is assigned to a developer during the triage process
2. Both MIEngine team members and external contributors are expected to do their work in a local fork and submit code for consideration via a pull request.
3. When the pull request process deems the change ready it will be merged directly into the tree. 

###Creating New Issues

Please follow these guidelines when creating new issues in the issue tracker:

- Use a descriptive title that identifies the issue to be addressed or the requested feature.
- Do not set any bug fields other than Impact.
- Specify a detailed description of the issue or requested feature.
- For bug reports, please also:
    - Describe the expected behavior and the actual behavior. If it is not self-evident such as in the case of a crash, provide an explanation for why the expected behavior is expected.
    - Provide example code that reproduces the issue.
    - Specify any relevant exception messages and stack traces.
- Subscribe to notifications for the created issue in case there are any follow up questions.

###Coding Conventions

- Use the coding style outlined in the [.Net Foundation Coding Guidelines](https://github.com/dotnet/corefx/wiki/Coding-style)
- Use plain code to validate parameters at public boundaries. Do not use Contracts or magic helpers.

```csharp
if (argument == null)
{
    throw new ArgumentNullException("argument");
}
```

- Use `Debug.Assert()` for checks not needed in retail builds. Always include a “message” string in your assert to identify failure conditions.

##Code Formatter

The MIEngine team regularly uses the [.NET code formatter tool](https://github.com/dotnet/codeformatter) to ensure the code base maintains a consistent style over time.  The specific options we pass to this tool are the following:

- `/nounicode`: In general we follow this rule of not having unicode characters embedded in string literals. However there are a few cases where this is needed to verify compiler behavior hence this option is disabled for now. 