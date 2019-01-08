---
layout: post
title: 'Azure Functions to the rescue: CSV to JSON'
date: 2018-03-03 10:08:01.000000000 -06:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:

tags:
- Azure
- Azure Functions
- C#
- Postman
- REST
author:
  #login: aaronralls74
  #email: arkeytekllc@gmail.com
  #display_name: aaronralls74
  #first_name: ''
  #last_name: ''
permalink: '/:year/:month/:day/:title'
---
<h1>Azure Functions: CSV to JSON</h1>
<p>Recently I had to create an SFTP server and update tables in <a href="https://docs.microsoft.com/en-us/azure/sql-database/">Azure SQL</a>. I decided to use an <a href="https://docs.microsoft.com/en-us/azure/logic-apps/">Azure Logic App</a> to check for new files and convert the data file from CSV to JSON and then insert them into the database using the <a href="https://docs.microsoft.com/en-us/azure/connectors/connectors-create-api-sqlazure">SQL Connector</a> and the Insert Row action.</p>
<p><img class="size-full wp-image-777" src="{{ site.baseurl }}/assets/images/20180303/Logic-App-Using-CSVToJSNON-function-1.png" alt="Azure Logic App using Azure Function CSVToJSON" width="683" height="666" /> <!--Azure Logic App using Azure Function CSVToJSON--></p>
<p>If your files are small you can call the CSV to JSON function from an Azure Logic App to process the file line by line. You could design your workflow and not have to write any other code!</p>
<h2>What are Azure Functions?</h2>
<p><a href="https://docs.microsoft.com/en-us/azure/azure-functions/">Azure Functions</a> are a serverless offering that can run code without having to set up infrastructure resources. They can automatically scale to support the workload needed and you only pay for them when they are used. In addition, the first <a href="https://azure.microsoft.com/en-us/pricing/details/functions/">1 million exections are FREE</a>!</p>
<p>If you have ever created or used an API from a third party you can think of each API call as its own Azure Function. It is a way of creating a <a href="https://azure.microsoft.com/en-us/blog/microservices-an-application-revolution-powered-by-the-cloud/">microservice architecture</a>.</p>
<h2>How can I use Azure Functions?</h2>
<ul>
    <li><u>From other Azure Functions</u> - This is useful to separate components and to make them scalable.</li>
</ul>
<ul>
    <li><u>Azure Logic Apps</u> - This is useful when you do not need to write code to perform a set of logical and repeatable steps. In the process flow, you may need to call an Azure Function to handle some specific set of logic.</li>
</ul>
<ul>
    <li><u>Azure Web Apps/API</u> - You may already have an Azure Web application or API that needs some new functionality, like resizing images. You could write a function to do this and call it from those applications.</li>
</ul>
<ul>
    <li><u>JavaScript Code</u> - You have a Web application or Mobile application that needs to send you error logs. You can write an Azure Function to accept the log contents and process them.</li>
</ul>
<h2>The Code</h2>
<p>The source code may be found <a href="https://github.com/aaronralls/FunctionAppCSVToJSON">here</a> on Github.</p>
<p>
    This is the class that will be our return object. This will have the rows of the original CSV data and the source file name.

    {% highlight csharp linenos %}
    {% raw %}
    public class JsonResult
    {
        public JsonResult(string fileName)
        {
            Rows = new List<object>();
            FileName = fileName;
        }

        public string FileName { get; set; }
        public List<object> Rows { get; set; }
    }
    {% endraw %}
    {% endhighlight %}
</p>
<p>
    This is an excerpt of the code from the Run method of our function class CSVToJSON. This shows how we instantiate the JsonResult object and add the data using the Rows property.

    {% highlight csharp linenos %}
    {% raw %}
    string[] csvLines = ToLines(csvData);

    var headers = csvLines[0].Split(fieldSeperator).ToList<string>();
    JsonResult resultSet = new JsonResult(fileName);

    foreach (var line in csvLines.Skip(rowsToSkip))
    {
        //Check to see if a line is blank.
        //This can happen on the last row if improperly terminated.
        if (line != "" || line.Trim().Length &gt; 0 )
        {
            var lineObject = new JObject();
            var fields = line.Split(fieldSeperator);

            for (int x = 0; x &lt; headers.Count; x++)
            {
                lineObject[headers[x]] = fields[x];
            }


            resultSet.Rows.Add(lineObject);
        }
        else
        {
            lineSkipCounter += 1;
        }
    }
    {% endraw %}
    {% endhighlight %}
</p>
<p>
    Finnally, this is the funtion that converts the CSV data from one large string into a string array to represent the rows of data in the original source.<br />
    {% highlight csharp linenos %}
    {% raw %}
    private static string[] ToLines(string dataIn)
    {
        char[] EOLMarkerR = new char[] { '\r' };
        char[] EOLMarkerN = new char[] { '\n' };
        char[] EOLMarker = EOLMarkerR;

        //check to see if the file has both \n and \r for end of line markers.
        //common for files comming from Unix\Linux systems.
        if (dataIn.IndexOf('\n') > 0 && dataIn.IndexOf('\r') > 0)
        {<br />
            //if we find both just remove one of them.<br />
            dataIn = dataIn.Replace("\n";, "");
        }
        //If the file only has \n then we will use that as the EOL marker to seperate the lines.
        else if(dataIn.IndexOf('\n') &gt; 0)
        {<br />
            EOLMarker = EOLMarkerN;
        }

        return dataIn.Split(EOLMarker);
    }
    {% endraw %}
    {% endhighlight %}
</p>
<h2>FunctionInput</h2>
<p>The JSON the function accepts consists of the following fields.</p>
<p>rowsToSkip - This indicates the number of rows to skip in the conversion process.</p>
<p>fileName - This is the source file where the data came from. This is passed in for downstream processes that may need to know which file was processed.</p>
<p>
    csv - This is the raw data from the source file that you want to be converted to JSON. This content may contain \r\n or \n end of line markers. The function will detect them an process the csv data accordingly.<br />
    {% highlight json linenos %}
    {% raw %}
    {
        "rowsToSkip": 1,
        "fileName": "MyTestCSVFile.csv",
        "csv":  "ID,Name,Score
    1,Aaron,99
    2,Dave,55
    3,Susy,77
    "
    }
    {% endraw %}
    {% endhighlight %}
</p>
<h2>Function Output</h2>
<p>The function will return the JsonResult object.</p>
<p>fileName - This is the source file where the data came from. This is passed in for downstream processes that may need to know which file was processed.</p>
<p>rows - list of the CSV data in JSON format, with the field name from the header row.</p>
<p>
    {% highlight json linenos %}
    {% raw %}
    {
        "fileName":"MyTestCSVFile.csv",
        "rows":[
    {
        "ID":"1",
        "Name":"Aaron",
        "Score":"99"
    },
    {
        "ID":"2",
        "Name":"Dave",
        "Score":"55"
    },
    {
        "ID":"3",
        "Name":"Susy",
        "Score":"77"
    }]
    }
    {% endraw %}
    {% endhighlight %}
</p>
<h2>Final Thoughts</h2>
<p>If you have larger files, like I did, you can use the <a href="https://docs.microsoft.com/en-us/sql/t-sql/functions/openrowset-transact-sql">SQL OPENROWSET</a> for bulk inserts or use the <a href="https://msdn.microsoft.com/en-us/library/system.data.sqlclient.sqlbulkcopy(v=vs.110).aspx">SqlBulkCopy class</a> to upload the data. The processing of the CSV data was similar and I used an Azure Function, BulkUpload, for that too. I will cover that in another blog post.</p>
<p><img class="size-full wp-image-779" src="{{ site.baseurl }}/assets/images/20180303/Logic-App-Using-Azure-Function-BulkUpload.png" alt="Azure Logic App Using Azure Function BulkUpload" width="683" height="699" /> <!--Azure Logic App Using Azure Function BulkUpload--></p>
