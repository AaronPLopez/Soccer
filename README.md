# Soccer
## Version 0.3.0.0
### Date: 2024/01/17

-------------------------------

### Soccer -- (Arc)SOC Scanner

#### Description 
Soccer is a utility scanning and reading the services' status on a specific ArcGIS Server folder. It then parses this data and writes it out to a CSV file for additional post-capture analysis. It takes advantage of the REST Admin's Service Report resource (https://developers.arcgis.com/rest/enterprise-administration/server/servicesreport.htm) in ArcGIS Server to gather this info. The original goal of soccer was to capture the report endpoint output of a specific folder in ArcGIS Server and save the ArcSOC instance statistics (e.g., Running, Busy, Maximum, etc...) for each service.
    
Currently, soccer is a command-line only utility.    
The tool is made available in the .NET 8.0 portable runtime for Windows (win-x64), Linux (linux-x64) and macOS (osx-x64).
Binaries for osx-arm64 and win-arm64 have been recently added (separate downloads).
    
### Download Latest Release
[Soccer Download](https://github.com/AaronPLopez/Soccer/raw/main/binaries/v0.3.0.0/soccer_v0.3.0.0.zip)

### System Requirements:
 - A 64bit Operating System that is supported by .NET 8.0 (https://dotnet.microsoft.com/en-us/download/dotnet/8.0)
	- .NET on Windows dependencies (https://docs.microsoft.com/en-us/dotnet/core/install/windows?tabs=net80#dependencies)
	- .NET on Linux dependencies (https://docs.microsoft.com/en-us/dotnet/core/install/linux)
	- .NET on macOS dependencies (https://docs.microsoft.com/en-us/dotnet/core/install/macos)
 - RAM: 4GB
    


#### Usage (Windows):
soccer.exe -s "[https://ArcGISServer/ServerWebAdaptor]" -f [FolderToScan] -t "[PreGeneratedArcGISToken]" -i [RefreshInSeconds] -o [OutputDirectoryForResults] -n [NameOfCSVFile] -a true
 - [https://ArcGISServer/ServerWebAdaptor] -- URL of ArcGIS Server's Web Adaptor resource, for example: https://gisserver.domain.com/server
 - [FolderToScan] -- ArcGIS Server folder to scan with each iteration (all services in this folder will be reported on, running or not running); to capture on the root folder use -f "" or -f /
 - [PreGeneratedArcGISToken] -- A common endpoint to pregenerate an ArcGIS Enterprise token from is: https://gisserver.domain.com/portal/sharing/rest/generateToken
 - [RefreshInSeconds] -- Refresh interval (in seconds) in which to re-read the Service Report resource and gather new statistics
 - [OutputDirectoryForResults] -- The directory path where the results file will be saved; will default to the "Documents\Soccer\Reports" directory; the directory will be created if it does not exist
 - [NameOfCSVFile] -- The name of the file to use for storing the results; if specifying a file name, include the .csv file extension (e.g., -n "myfile.csv"); will default to a "unique" file starting with "Service_Report-" and ending with a datetime suffix
 - [Append] -- Passing in "-a true" with the "-n [NameOfCSVFile]" option will append results to an existing data file

##### Usage Examples (Windows):
soccer.exe -s "https://gisserver.domain.com/server" -f MAPS -t "SqyWOcKZp9stZ_C01DQ.." -i 5



#### CSV Output
The CSV file writes over 20 data columns with every iteration (default is every 5 seconds). Every service folder in the folder of interest will get a line written to the CSV file. 
Data fields like: "DateTime,Epoch,IntervalSeconds,Folder and Message" are the same for all the services. The Message column reports a "success" if the service report endpoint could 
be read or a brief reason why it could not.
Unique fields (per each service) include information such as:
 - The Service details (e.g., name, type, realtime state)        
 - ArcSOC Instance details (e.g., running, busy, maximum, not created)
	- This info provides a view into the lifecycle of all instances of the service on all server machines within the cluster
 - Response time and content length of the capture
 - ArcGIS Server's transaction and totalbusytime 
	- The transactions metric indicates the total number of invocations that have occurred on the service
	- The totalBusyTime metric indicates the total amount of time the service is in use servicing requests.
	- See https://resources.arcgis.com/en/help/server-admin-api/serviceStatistics.html for more information

#### CSV Sample:
###### DateTime,Epoch,IntervalSeconds,Host,Folder,ServiceName,Type,Provider,Running,Busy,Maximum,Free,NotCreated,Initializing,Transactions,TotalBusyTime,ServicesCollected,ResponseTimeMilliseconds,ContentLength,ConfiguredState,RealTimeState,Message
12/29/2022 12:36:21 AM,1672274181104,20,gisserver.domain.com,MAPS,AverageDailyTraffic,MapServer,ArcObjects11,0,0,0,0,0,0,0,0,3,2012.1462,8762,STOPPED,STOPPED,success
12/29/2022 12:36:21 AM,1672274181104,20,gisserver.domain.com,MAPS,CitrusIrrigation,MapServer,ArcObjects11,2,0,10,2,8,0,0,0,3,2012.1462,8762,STARTED,STARTED,success
12/29/2022 12:36:21 AM,1672274181104,20,gisserver.domain.com,MAPS,TrafficSignalLights,MapServer,ArcObjects11,3,0,10,3,7,0,0,0,3,2012.1462,8762,STARTED,STARTED,success



-------------------------------

##### CHANGELOG

Build 0.3.0.0 (Prerelease)
1. Port of Soccer to .NET 8.0

Build 0.2.1.0 (Prerelease)
1. Fixed an issue that prevented soccer from collecting against ArcGIS Server on port 6443
2. Minor simplification of the internal cookie that gets passed to ArcGIS Server
3. Added condition specific error messages to the recorded output, if they are encountered
4. Due to a api.nuget.org issue on win-x64 binaries are available for this release

Build 0.2.0.0 (Prerelease)
1. Enhanced the verbosity of the message field for errors to provide more information
2. Added the append option (-a) to reuse a previously generated data file; must be used with the output filename (-n) option
3. The output file now lists the passed in host and folder values even if soccer could not connect to the remote host
4. Added more information to the console as soccer runs
  
Build 0.1.0.2 (Prerelease)
1. Fixed typo in the command-line help that would report the incorrect version for soccer
2. Binary build using the .NET 6 SDK 6.0.413 (.NET Runtime 6.0.21)

Build 0.1.0.1 (Prerelease)
1. Fixed a condition that could cause soccer to crash if the ArcGIS Server itself went offline or was unavailable

Build 0.1.0.0 (Prerelease)
1. Initial release
