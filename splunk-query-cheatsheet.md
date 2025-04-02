# Splunk Search Query Cheat Sheet

## Basic Search Syntax

### Simple Search Terms
```
error                  # Search for the term "error"
"internal server error" # Search for the exact phrase
error OR failure       # Search for either term
error AND failure      # Search for both terms
error NOT "timeout"    # Search for error but not timeout
(error OR failure) NOT timeout  # Grouping with parentheses
```

### Wildcards and Operators
```
fail*                 # Wildcard - matches "fail", "failed", "failure", etc.
app_*                 # Matches fields starting with "app_"
*_id                  # Matches fields ending with "_id"
te?t                  # Single character wildcard - matches "test", "text", etc.
status=500            # Field equals value
status!=500           # Field does not equal value
status>500            # Greater than
status>=500           # Greater than or equal to
status<500            # Less than
status<=500           # Less than or equal to
status=200 OR status=300  # Multiple conditions
```

### Boolean Operators
```
AND   # Both conditions must be true (default operator between terms)
OR    # Either condition must be true
NOT   # Negates a condition
()    # Group expressions to control precedence
```

## Time Range Selection

### Relative Time Modifiers
```
earliest=-24h         # Last 24 hours
latest=now            # Until now
earliest=-7d@d        # Starting 7 days ago, at midnight
earliest=-1w@w        # Starting at the beginning of previous week
latest=-1d@d          # Until yesterday at midnight
earliest=@d           # Starting at midnight today
earliest=-30m@m       # Starting 30 minutes ago, aligned to minute
earliest=-1h@h latest=@h  # Previous full hour

# Common time units
s = seconds
m = minutes
h = hours
d = days
w = weeks
mon = months
y = years
```

### Absolute Time
```
earliest=01/15/2023:12:00:00  # Specific start time
latest=01/15/2023:23:59:59    # Specific end time
```

### Time Modifiers in Search Bar
```
error earliest=-12h latest=-1h  # Errors from 12 hours ago until 1 hour ago
error earliest=-7d@d latest=@d  # Errors from last week until today at midnight
```

## Field Search

### Field Extraction and Use
```
host="webserver01"                    # Exact match on field
host="webserver*"                     # Wildcard match on field
host IN ("webserver01", "webserver02") # Multiple possible values
host=webserver* NOT host=*test*       # Combined field conditions
source="/var/log/system.log"          # Source field
sourcetype="access_combined"          # Sourcetype field
index="main"                          # Specific index
index=web* OR index=app*              # Multiple indices with wildcard
```

### Working with Fields
```
* | fields host, source, sourcetype   # Extract only specific fields
* | fields - host, source             # Remove specific fields
* | fieldsummary                      # Summarize fields in your results
* | fields + ip                       # Add a specific field to results
```

### Multi-value Fields
```
tags="error"                  # Field contains "error"
tags IN ("error", "warning")  # Field contains any of the values
tags=*                        # Field exists and is not null
NOT tags=*                    # Field does not exist or is null
mvcount(tags)>1               # Field has multiple values
mvindex(tags, 0)="error"      # First value is "error"
mvfind(tags, "err")>=0        # Field contains substring "err"
```

## Core SPL Commands

### Data Filtering and Manipulation
```
| where status>=500                   # Filter results using expressions
| where like(url, "%.php%")           # String comparison with like()
| rename status AS http_status        # Rename fields
| eval is_error=if(status>=400, 1, 0) # Create calculated fields
| eval total=price*quantity           # Arithmetic operations
| eval timestamp=strftime(_time, "%Y-%m-%d") # Format time
| eval size_mb = round(size/1024/1024, 2)    # Convert and round
| fieldformat timestamp=strftime(_time, "%Y-%m-%d") # Format display without creating new field
```

### Search Results Control
```
| head 10                     # Return only first 10 results
| tail 20                     # Return only last 20 results
| sort -timestamp             # Sort by timestamp descending
| sort +response_time         # Sort by response_time ascending
| sort -severity, +timestamp  # Sort by multiple fields
| dedup hostname              # Remove duplicate events based on hostname
| dedup user_id sortby -_time # Keep newest event for each user_id
| dedup 3 ip_address          # Keep up to 3 events per ip_address
| limit 100                   # Limit results to 100 events
```

## Statistical Analysis

### Basic Statistics
```
| stats count                   # Count events
| stats count BY host           # Count events by host
| stats dc(clientip)            # Count distinct client IPs
| stats count, dc(url) BY host  # Multiple stats by host

| stats sum(bytes) as total_bytes          # Sum of bytes
| stats avg(response_time) as avg_response # Average response time
| stats median(response_time) as med_response # Median response time
| stats min(response_time) as min_response   # Minimum value
| stats max(response_time) as max_response   # Maximum value
```

### Aggregation Commands
```
| stats count BY host, source    # Group by multiple fields
| chart count BY host            # Create table with count per host
| chart sum(bytes) BY host       # Sum of bytes by host
| chart avg(response_time) OVER app BY host # Table with apps as rows and hosts as columns
| timechart span=1h count        # Count of events per hour
| timechart span=15m avg(cpu_percent) BY host # Avg CPU by host per 15 min
| top 10 user_agent              # Find top 10 user agents
| top limit=10 url by method     # Top URLs by HTTP method
| rare 10 status                 # Find 10 least common status codes
| eventstats avg(response_time) as avg_resp BY host # Compute stats and keep original events
```

### Time-based Commands
```
| timechart span=1h count                      # Count events per hour
| timechart span=1d sum(bytes) as traffic      # Daily traffic sum
| timechart span=5m count by status            # Count by status every 5 min
| timechart span=1h avg(response_time)         # Hourly average response time
| timechart span=1d max(cpu_percent) limit=5   # Daily max CPU, top 5 series

| bin span=5m _time                # Group events into 5-minute bins
| bucket _time span=1h             # Alias for bin
| delta bytes                      # Calculate change between events
| trendline sma5(cpu_percent)      # 5-point simple moving average
```

## Visualizations

### Chart Commands
```
| chart count over host by status   # Table with hosts as rows, status as columns
| chart sum(bytes) AS "Total Bytes" by host  # Custom column name

# Visualization formatting
| chart count BY host useother=t limit=5  # Group hosts beyond top 5 as "OTHER"
| chart count BY host useother=f          # Don't display other values
| chart count BY host useother=f otherstr="Everything Else"  # Custom other name

# Advanced charting
| chart eval(round(avg(response_time),2)) AS "Avg Response Time" BY host  # Calculations
| chart count BY host WITH min(response_time) AS "Min" max(response_time) AS "Max"  # Multiple metric
```

### Other Visualizations
```
| geostats latfield=lat longfield=lon count by status  # Geographic visualization
| iplocation clientip                                  # Enrich with IP location data
| choropleth count by client_country                   # Country-colored map
| tscollect                                            # Time series collection
| gauge count min=0 max=1000                           # Gauge visualization
| radial count by host                                 # Radial visualization
```

## Enriching and Manipulating Data

### Lookups
```
| lookup countries_lookup ip AS clientip OUTPUT country   # Add country field from lookup
| lookup local=true dnslookup clientip                    # DNS lookup
| inputlookup users.csv | where department="Engineering"  # Query lookup table directly
| outputlookup updated_users.csv                          # Save results to lookup
```

### Field Extraction
```
| rex field=_raw "User: (?<username>[^ ]+)"      # Extract username from raw text
| rex field=request_url "category=(?<category>[^&]+)"  # Extract from URL parameter
| regex _raw="Failed login for (?<username>\w+)"  # Another way to extract with regex
| erex examples="Failed login: jsmith"            # Extract with examples
```

### Calculations and Transformations
```
| eval is_slow=if(response_time>1, "Yes", "No")  # Create field based on condition
| eval severity=case(
    status>=500, "Critical", 
    status>=400, "Warning", 
    status>=300, "Info", 
    1=1, "Normal")                               # Multiple conditions
| eval full_name=user_first + " " + user_last    # Concatenate fields
| eval timestamp=strftime(_time, "%Y-%m-%d %H:%M:%S")  # Format time
| eval day_of_week=strftime(_time, "%A")         # Get day name
| eval size_mb=round(size/1024/1024, 2)          # Convert and round
| eval result=if(isnull(field), "N/A", field)    # Handle null values
```

### String Functions
```
| eval user=lower(username)                 # Convert to lowercase
| eval server=upper(host)                   # Convert to uppercase
| eval url_parts=split(request_url, "/")    # Split string into array
| eval domain=mvindex(url_parts, 2)         # Extract item from array
| eval file_ext=mvindex(split(file, "."), -1)  # Get file extension
| eval cleaned=trim(field)                  # Remove whitespace
| eval length=len(message)                  # String length
| eval contains_error=if(match(message, "(?i)error"), 1, 0)  # Case-insensitive match
| eval subdomain=replace(hostname, "\.example\.com$", "")  # String replacement
```

### Time Functions
```
| eval epoch_time=strptime(timestamp, "%Y-%m-%d %H:%M:%S")  # Convert string to epoch time
| eval formatted_time=strftime(_time, "%Y-%m-%d")           # Format epoch time
| eval time_diff=(_time - prev_time)                        # Time difference in seconds
| eval hours_ago=round((_time - request_time)/3600, 1)      # Hours between events
| eval now_epoch=now()                                      # Current time in epoch
| eval is_old=if(_time < relative_time(now(), "-7d@d"), 1, 0)  # Compare with relative time
| eval day_name=strftime(_time, "%A")                       # Day of week name
| eval month_name=strftime(_time, "%B")                     # Month name
| eval quarter=ceil(strftime(_time, "%m")/3)                # Calculate quarter
```

## Advanced Search Techniques

### Subsearches
```
| where host IN [ search sourcetype=inventory | fields host ]  # Host list from subsearch
index=web [ search index=security sourcetype=alerts | fields src_ip | rename src_ip as clientip ]
                                                              # Events with IP from security alerts
index=network [ search index=security sourcetype=alerts earliest=-24h 
    | stats count by src_ip | where count > 5 | fields src_ip ] # IPs with many alerts
```

### Transactions
```
| transaction sessionid                   # Group events by session
| transaction sessionid maxspan=5m        # Session events within 5 min of each other
| transaction sessionid startswith="login" endswith="logout"  # Session from login to logout
| transaction clientip, url maxspan=1h maxevents=100          # Group by multiple fields
| transaction JSESSIONID maxpause=30m     # Allow 30-min pauses within transactions
| stats count, avg(duration) by host      # Analyze transaction duration
```

### Joins and Merges
```
| join type=inner userid [ search index=security | fields userid, access_level ]
                                          # Inner join with security data
| join type=left user_id 
    [ search index=user_db | rename id as user_id | fields user_id, dept, manager ]
                                          # Left join with user database
| join type=outer clientip
    [ search index=blocklist | fields clientip, reason ] # Outer join with blocklist
| append
    [ search index=historical_data earliest=-1y latest=-1mon ] # Append historical data
| appendpipe
    [ stats count by host | sort - count ] # Append summary of results
| union
    [ search index=web ] [ search index=app ] [ search index=api ] # Combine multiple searches
```

### Managing Events Across Time
```
| streamstats count as event_sequence by session_id # Number events within session
| streamstats current=f window=5 avg(cpu_percent) as rolling_avg by host # Rolling avg of previous 5 events
| streamstats current=t global=t count as global_count # Count all events up to current
| streamstats current=f window=5 range(_time) as time_span by host # Time range of 5 events

| eventstats avg(response_time) as avg_resp by host # Avg response time by host
| eventstats min(_time) as session_start max(_time) as session_end by session_id # Session time range
| eventstats dc(url) as unique_urls by user_id # Count unique URLs per user

| addinfo # Add search metadata like earliest/latest time
| localize # Convert UTC timestamps to local time
```

## Regular Expressions in Splunk

### Common Regex Patterns
```
[a-zA-Z0-9]+              # Alphanumeric characters (one or more)
\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}  # Simple IP address pattern
[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}  # Email address
(GET|POST|PUT|DELETE)     # HTTP methods
"status":\s*(\d+)         # JSON status field with value
\[(\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2})\]  # Log timestamp [YYYY-MM-DD HH:MM:SS]
(?i)error                 # Case-insensitive "error"
```

### Regex in Splunk Commands
```
| rex field=request "(?<method>GET|POST|PUT|DELETE) (?<url>[^ ]+)"  # Extract HTTP method and URL
| regex _raw="Failed login for (?<username>\w+)"                    # Extract with regex command
| where match(_raw, "(?i)error|exception|fail")                     # Filter with regex match
| where like(url, "%.php%")                                         # Simple pattern matching
```

## Scheduled Searches, Alerts and Dashboards

### Search Scheduling Options
```
cron=0 */6 * * *            # Every 6 hours
cron=0 8 * * 1-5            # 8 AM on weekdays
schedule=hourly              # Every hour
```

### Alert Actions
```
counttype=number            # Alert based on result count
counttype=custom result_field  # Alert based on specific field
```

### Dashboard and Panel Options
```
<query>search index=web status>=500</query>
<earliest>-24h@h</earliest>
<latest>now</latest>
<option name="drilldown">none</option>
<option name="refresh.auto.interval">60</option>  # Auto-refresh every minute
```

## Macros and Knowledge Objects

### Using Macros
```
`error_filter`                      # Use a macro
`response_time_thresholds(500, 1000)`  # Macro with arguments
| `calculate_response_percentiles`     # Macro as a pipeline command
```

### Defining Macros
```
error_filter = status>=500 OR severity IN ("Error", "Critical", "Fatal")
response_time_thresholds(slow_threshold, critical_threshold) = eval response_level=case(response_time>=$critical_threshold$, "critical", response_time>=$slow_threshold$, "slow", 1=1, "normal")
```

### Tags and Event Types
```
tag::network=sourcetype=netflow OR sourcetype=cisco:asa    # Tag definition
tag=network                                               # Search using tag
eventtype=failed_login                                    # Search using event type
```

## Optimizing Performance

### Search Optimization
```
index=web status=404                # Use index and fields (faster than text search)
index=web status=404 earliest=-1d@d latest=@d  # Explicit time restrictions
index=web source="/var/log/apache/access.log" NOT debug  # Narrow scope with NOT
```

### Performance Commands
```
| tstats count WHERE index=web by host, status     # Use tstats for better performance
| datamodel Network_Traffic search | stats count by src_ip  # Search from accelerated data model
| prefix="||" X-Forwarded-For clientip src_ip          # Search multiple fields at once
| sistats count by clientip                        # Summary indexed statistics
```

### Distributed Search
```
| map search="search index=web host=$host$" maxsearches=50  # Parallel search per host
```

## Troubleshooting Searches

### Debug Commands
```
| debug [option=value]                  # Debug a search
| metadata type=sourcetypes index=web   # List sourcetypes in an index
| eventcount summarize=false index=*    # Count events across all indexes
| typeahead prefix=fail                 # Check for matching terms
| bucket _time span=1m                  # Check time bucketing
```

### Search Job Inspector
```
mode=verbose         # Detailed search job inspection
remote_server=true   # Include remote server info
```

## Advanced SPL Examples

### Security Use Cases
```
# Find brute force login attempts
index=security sourcetype=auth "Failed password" 
| stats count by src_ip, user 
| where count>5

# Detect anomalous authentication
index=security sourcetype=auth action=success 
| bucket span=1h _time 
| stats count by _time, src_ip, user 
| eventstats avg(count) as avg_count, stdev(count) as stdev_count by user, src_ip 
| where count > avg_count+3*stdev_count

# Track privilege escalation
index=security sourcetype=auth "sudo" 
| stats count, values(command) as commands by src_user, dest_user, host
```

### IT Operations Use Cases
```
# System resource monitoring
index=linux sourcetype=cpu 
| timechart span=5m avg(usage) by host 
| eval alert=if(avg>90, "Critical", "Normal") 
| sort -avg

# Service availability tracking
index=app sourcetype=availability 
| timechart span=1h avg(response_time) as avg_resp, count as checks, eval(count(eval(status="Down"))) as outages by service 
| eval availability=round(100-((outages/checks)*100), 2)

# Capacity planning
index=network sourcetype=bandwidth 
| bucket _time span=1d 
| stats avg(utilization) as daily_avg, max(utilization) as daily_peak by _time, device 
| stats avg(daily_avg) as avg, avg(daily_peak) as peak, max(daily_peak) as max_peak by device 
| eval trend=if(avg/peak > 0.7, "Upgrade needed", "OK")
```

### Application Performance Use Cases
```
# Slow transaction tracking
index=web sourcetype=access_combined 
| transaction JSESSIONID maxspan=5m 
| where duration>10 
| stats count, avg(duration) as avg_duration, values(uri_path) as paths by JSESSIONID, clientip

# Error rate analysis
index=app sourcetype=application 
| bucket span=5m _time 
| stats count(eval(level="ERROR")) as errors, count as total by _time, application 
| eval error_rate=round((errors/total)*100,2) 
| timechart avg(error_rate) by application

# User experience monitoring
index=web sourcetype=access_combined 
| iplocation clientip 
| stats avg(response_time) as avg_resp, median(response_time) as med_resp, p95(response_time) as p95_resp by Country 
| sort -avg_resp
```

## Special Search Commands

### REST and External Data
```
| rest /servicesNS/-/-/saved/searches           # Query Splunk REST API
| rest /services/authentication/users           # List Splunk users
| inputcsv users.csv | eval status=if(last_login < relative_time(now(), "-90d"), "inactive", "active")  # Process CSV
| inputlookup geo_data.csv | where country="USA" | stats count by state  # Query lookup table
| outputcsv export_filename.csv                 # Export results to CSV
| sendemail to="user@example.com" subject="Splunk Alert"  # Send email with results
```

### Data Manipulation and Modeling
```
| makeresults count=24                           # Generate sample events
| eval _time=_time - 3600*({n}-1)                # Create events with timestamps
| streamstats count                              # Add sequence counter
| eval server="server".random()%10               # Generate random values
| eval value=random()%100                        # Random metrics

| fit LinearRegression "response_time" from "concurrent_users" "server_load"  # Regression model
| fit XGBoost is_error from "url" "user_agent" "status" into model_name       # Machine learning model
| anomalies count as anomaly_count                                            # Detect anomalies
| cluster field=message showcount=true                                        # Cluster similar events
```

### Machine Data Commands
```
| kmeans k=5 response_time, error_count, cpu_load  # k-means clustering
| anomalousvalue action=remove field=response_time pthresh=0.01  # Remove anomalies
| anomalydetection response_time                    # Find unusual values

| predict response_time algorithm=LL future_timespan=24h holdback=1d           # Forecast values
| predict response_time algorithm=LLP future_timespan=24h              # Forecast with periodicity
| x11 response_time                                                    # Time series decomposition
```

## Workflow and Collaboration

### Knowledge Object Management
```
| datamodel                                 # List data models
| datamodel Network_Traffic search          # Search accelerated data model
| savedsearch name=search_name              # Run saved search
| typelearner                               # Generate field extractions
| typelearner field=message                 # Field-specific extractions
| archive                                   # Archive search results
```

### Collaboration
```
| sendemail to="team@example.com" subject="Alert: High Error Rate" message="See attached results" results_link=true  # Email with link
| sendemail to="team@example.com" subject="Daily Report" inline=true sendresults=true   # Email with inline results
| sendalert                                 # Trigger alert actions
| makeresults | fields - _* | eval message="Job completed at ".strftime(now(), "%c")  # Status message
```

## ITSI and Enterprise Security Commands

### ITSI (IT Service Intelligence)
```
| tstats latest(_time) as _time, values(Authentication.user) as user where index=* by host  # Time-based stats
| pivot Splunk_SA_CIM Authentication latest(_time) as _time, values(user) as user by host  # Data pivot
| itsi_get_entities                         # Get ITSI entities
| itsi_get_service depend=1                 # Get service dependencies
| itsi_get_entity_ids search="host=*web*"   # Search for entities
```

### Enterprise Security
```
| iplocation clientip                       # Add geo information
| whois domain                              # Domain lookup
| nslookup clientip                         # DNS lookup
| lookup threat_list ip_address as clientip # Check against threat list
| lookup identity_lookup user                # Identity lookup

| risk source=brute_force                    # Add to risk index
| risk score=70 object_type=system object=web01 source=correlation  # Risk scoring
| risk_summary user=* limit=10               # Risk summary
| notable                                    # Create notable event
```
