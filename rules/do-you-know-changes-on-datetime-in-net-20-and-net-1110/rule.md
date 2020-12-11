---
type: rule
archivedreason: 
title: Do you know changes on Datetime in .NET 2.0 and .NET 1.1/1.0
guid: aae59cde-200b-4d55-90b1-ed0db45d32af
uri: do-you-know-changes-on-datetime-in-net-20-and-net-1110
created: 2009-05-08T08:37:20.0000000Z
authors:
- id: 1
  title: Adam Cogan
- id: 17
  title: Ryan Tee
related: []

---

In v1.0 and v1.1 of .NET framework when serializing DateTime values with the XmlSerializer, the local time zone of machine would always been appended. And when deserializing on the receiving machine, DateTime values would be automatically adjusted based on time zone offset relative to the sender time zone. See below example:  
<!--endintro-->

<dl class="goodCode">    <dt style="width&#58;91.71%;height&#58;68px;">
    <pre>DataSet returnedResult = webserviceObj.GetByDateCreatedAndEmpID(DateTime.<br>Now,'JZ');                            </pre>
    &lt;/dt&gt;
</dt></dl>**Figure: front end code in .NET v1.1 (front end time zone: GTM+8)** 
<dl class="goodCode">    <dt style="width&#58;92.48%;height&#58;168px;">
    <pre>[WebMethod] public DataSet GetByDateCreatedAndEmpID(DateTime DateCreated, String                                <br>EmpID)                                <br>&#123;<br>     EmpTimeDayDataSet ds = new EmpTimeDayDataSet();                                <br>     m_EmpTimeDayAdapter.FillByDateCreatedAndEmpID(ds, DateCreated.Date, EmpID);                                <br>     return ds;<br>&#125;                            </pre>
    &lt;/dt&gt;
</dt></dl>
**Figure: web service method (web service server time zone: GTM+10)**

When front end calls this web method with the value of current local time (14/01/2006 11:00:00 PM GTM+8) for parameter 'DateCreated', it expects a returned result for date 14/01/2006, while the service end returns data of 15/01/2006, because 14/01/2006 11:00:00 PM (GTM+8) would be adjusted to be 15/01/2006 01:00:00 AM at the web service server (GTM+10)

In v1.1/v1.0 you have no way to control this serializing/deserializing behaviour on DateTime. In v2.0 with the new notion DateTimeKind you can get a workaround for above example,
<dl class="goodCode">    <dt style="width&#58;92.32%;height&#58;89px;">
    <pre>Datetime unspecifiedTime = DateTime.SpecifyKind(DateTime.Now,DateTimeKind.<br>Unspecified);                                <br>DataSet returnedResult = webservceObj.serviceObj.GetByDateCreatedAndEmpID,<br>(unspecifiedTime,'JZ');                            </pre>
    &lt;/dt&gt;
</dt></dl>
**Figure: front end code in .NET v2.0 (front end time zone: GTM+8)**

In this way, the server end will always get a datetime value of 14/01/2006 11:00:00 without GTM offset and return what front end expects