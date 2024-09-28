# Soccer
## Version 0.3.2.1
### Date: 2024/09/27

-------------------------------

### Soccer -- (Arc)SOC Scanner

#### Description 
Soccer is a utility scanning and reading the services' status on a specific ArcGIS Server folder. It then parses this data and writes it out to a CSV file for additional post-capture analysis. It takes advantage of the REST Admin's Service Report resource (https://developers.arcgis.com/rest/enterprise-administration/server/servicesreport.htm) in ArcGIS Server to gather this info. The original goal of soccer was to capture the report endpoint output of a specific folder in ArcGIS Server and save the ArcSOC instance statistics (e.g., Running, Busy, Maximum, etc...) for each service.
    
Currently, soccer is a command-line only utility.    
The tool is made available in the .NET 8.0 portable runtime for Windows (win-x64), Linux (linux-x64) and macOS (osx-x64).
Binaries for osx-arm64 and win-arm64 have been recently added (separate downloads).
    
### Download Latest Release
[Soccer Download](https://github.com/AaronPLopez/Soccer/raw/main/binaries/v0.3.2.1/soccer_v0.3.2.1.zip)

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
 - [MaximumIterations] -- A numeric value which specifies how many times soccer should report on the specified folder
 - [LogLevel] -- Whether or not to record application runtime information to a separate log file in the report folder (default is OFF (silent); other options include: ERROR, WARN, INFO, DEBUG, TRACE)
 - [-a true] -- Boolean, whether or not to append the output to an existing data file (default is false, if true, this parameter must be used with the -n [NameOfCSVFile] option)
 - [-c true] -- Boolean, whether or not to send the Accept-Encoding header to request the compression of the server responses (default is true)

##### Usage Examples (Windows):
soccer.exe -s "https://gisserver.domain.com/server" -f MAPS -t "SqyWOcKZp9stZ_C01DQ.." -i 5
soccer.exe -s "https://gisserver.domain.com/server" -f MAPS -t "SqyWOcKZp9stZ_C01DQ.." -i 5000 -m 10 -apploglevel debug -c true



#### CSV Output
The CSV file writes over 20 data columns with each iteration (default is every 5 seconds). A data line will be written to the CSV file with information on each service that exists in the customizable folder of interest. 
Data fields like: "DateTime,Epoch,IntervalMillisecond,Folder and Message" are the same for all the services. The Message column reports a "success" if the service report endpoint could 
be read or a brief reason why it could not. No information will be written if no services exist in the folder. The DateTime field is in UTC.
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
###### DateTime,Epoch,IntervalMilliseconds,Host,Folder,ServiceName,Type,Provider,Running,Busy,Maximum,Free,NotCreated,Initializing,Transactions,TotalBusyTime,ServicesCollected,ResponseTimeMilliseconds,ContentLength,ConfiguredState,RealTimeState,Message
12/29/2022 12:36:21 AM,1672274181104,5000,gisserver.domain.com,MAPS,AverageDailyTraffic,MapServer,ArcObjects11,0,0,0,0,0,0,0,0,3,312,8762,STOPPED,STOPPED,success
12/29/2022 12:36:21 AM,1672274181104,5000,gisserver.domain.com,MAPS,CitrusIrrigation,MapServer,ArcObjects11,2,0,10,2,8,0,0,0,3,312,8762,STARTED,STARTED,success
12/29/2022 12:36:21 AM,1672274181104,5000,gisserver.domain.com,MAPS,TrafficSignalLights,MapServer,ArcObjects11,3,0,10,3,7,0,0,0,3,312,8762,STARTED,STARTED,success



-------------------------------

##### CHANGELOG

Build 0.3.2.1 (Prerelease)
1. Fixed an issue with the report folder data where the Iteminfo's minScale property was defined as an int instead of a double, this caused the parsing of some services to fail where the data of this property was a numeric double
2. Fixed an issue where soccer no longer lists an empty folder (e.g., one that contains no services) as an error in the application log (e.g., the optional runtime log for troubleshooting); if soccer is configured to analysis a folder that is empty (at the time of each collect), nothing other than the column header will appear in the csv output file

Build 0.3.2.0 (Prerelease)
1. Added some verbosity to the application log (e.g., HTTP version, HTTP response header key/value pairs, ArcGIS Server version)
2. Added ability of the internal HTTP framework to automatically perform decompression of the server's response
3. Added a "-c [bool]" option, which tells the HTTP client (e.g., soccer) whether or not to send the Accept-Encoding header compressing the server's response; the default is true (which is to request that the response is compressed)
   
Build 0.3.1.0 (Prerelease)
1. The "-i [numeric value]" option, "Interval in seconds" has now been changed to Interval in milliseconds to allow for finer grained control of the collection precision
   Previous scripts which passed in a parameter of "-i 5" will need to update it to reflect "-i 5000", otherwise the script will run too quickly
2. A maximum iteration option "-m [numeric value]", has been added to have Soccer exist after checking a folder a specified number of times; the default is unlimited iterations
3. Application debug logging added (via NLog) through the option "-apploglevel [LogLevel value]"; choices are OFF (default), ERROR, WARN, INFO, DEBUG, TRACE
   The application logging is currently synchronous, so using DEBUG or TRACE can affect reporting performance of Soccer
4. Underneath the hood, the measurement of ResponseTimeMilliseconds has been changed from TotalMilliseconds to Milliseconds; the end result is a slight change in the precision of the represented millisecond value (e.g., 202 vs 202.2088)
5. Added the display of the MaximumIterations option to console output
   
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
