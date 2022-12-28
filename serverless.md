# Resources

- [Azure Function Core Tools](https://learn.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=v4%2Cwindows%2Ccsharp%2Cportal%2Cbash)


# Serverless

1. Set up the lab environment.
- Download and install [Azure Function](https://go.microsoft.com/fwlink/?linkid=2174087) and [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools/releases/tag/4.0.4865)
	- Azure function core tools enable us to develope and test our functions on the local machine using windows terminal. The functions can connect to Azure services, and can be debuged on local machine using full Function runtime.
- Open a new `wt.exe` instance and verify that the tools have been installed successfully.
```powershell
funch --version
```

- Download and install Node.js 8+
- Verify the installation
```powershell
node --version
npm --version
```

- Download and install [.Net 6 SDK](https://dotnet.microsoft.com/en-us/download)
- Verify the installation
```powershell
dotnet --version
```

2. Create a "Hello World" Azure Function in VSCode.
- Open VSCode and click on the Azure Tools extension.
- In the left nav, click on the `+` icon next to *WORKSPACE Local* to create the first function.
![[Pasted image 20221227160711.png]]

- 