# HRPartner2GoogleSheets
A custom script module to extract data from HR Partner an populate a Google Sheet

This module gives you an easy custom Google Sheets function for extracting data from your HR Partner company (using our API) and placing that information into a Sheet that you can do further manipulations on.

Note: You must already have an HR Partner company set up at https://www.hrpartner.io (trial or fully paid subscription) in order for this script to work for you.

## Installing The Custom Script

All you need to do is to click on the `HRPartner2GoogleSheets.gs` file above, and copy the entire contents of that file into your clipboard.

Then, open a new or existing Google Sheet Workbook in your browser, and click on '_Tools_', then '_Script Editor_'.  This will open up a new browser tab with the Google Script Editor, with a tab called '_**Code.gs**_' opened with a small sample script in there.

Paste the copied information into the '_Code.gs_' editor tab, over writing anything that was already there.

> IMPORTANT: You need to now edit the pasted script to insert your own API Key for your HR Partner company.  Full instructions on activating the API access for your HR Partner company and obtaining your key are on [this help documentation page](https://help.hrpartner.io/article/80-about-our-api).

Once you have pasted your key into line 39 of the script, you can continue.

Click on the 'Save' icon (or select 'File' then 'Save') in the Script Editor.

Now you can go back to the tab with your worksheet on it and begin playing.

## API Documentation

The documentation for our API, which you will need to refer to in order to get the correct module names mentioned below, as well as the query parameters and field names, are all available at:  https://developer.hrpartner.io

## Using The Script

This custom script basically adds two new functions into your Google Sheet system.  These are:-

__ImportHRpartner()__ - Function to import virtually any data from HR Partner into a Sheet.

__DateHRPartner()__ - Function to convert the date & date/time strings returned from HR Partner into proper Google Sheet date objects.

These can be used like any other Google Sheet function, by entering in `=ImportHRPartner(<parameters>)` into a cell.

### ImportHRPartner()

To import data from HR Partner, simply go to any cell in a sheet that will be the top left point of all the data returned from HR Partner  (Note: When the function runs, it will appear to have over-written this formula, but it will still be there when you click on the top, leftmost cell in the returned data).

Next, enter in the formula:

```
=ImportHRPartner('<module>','<fields>','<options>')
```

The parameters are explained below:

`<module>` - This mandatoryparameter specifies the module from HR Partner which you wish to pull information from.  For example: employees, absences, reminders, training etc. (see our API documentation mentioned above).  This parameter can also contain query filters which will only extract certain subsets of data.

`<fields>` - This optional parameter can contain the list of field objects that you wish to return.  If you don't specify this parameter, then ALL fields for the module are returned to you.  See examples below for more information

`<options>` - This optional parameter specifies any special formatting that will be carried out on the returned data.  These can be:
 * **noInherit** - Don't inherit values from parent elements
 * **noTruncate** -Don't truncate values
 * **rawHeaders** - Don't prettify headers
 * **noHeaders** - Don't include headers, only the data
 * **allHeaders** - Include all headers from the query parameter in the order they are listed

#### Examples

`ImportHRPartner('employees')`

This will import ALL employees from your HR Partner company, and populate your sheet with ALL returned fields.

`ImportHRPartner('employees?department=Finance')`

This will import all employee within the department called '_Finance_' and populate your sheet with ALL returned fields.

`ImportHRPartner('employees?department=Operations&location=New York', '/code,/full_name,/date_of_birth')`

This will import only 3 fields from HR Partner, the _code_,_full name_, and _date of birth_ for any employee in the '_Operations_' department and in the '_New York_' location into your sheet.

`ImportHRPartner('absences?employee=A1234', '/absence_reason,/description,/absence_date,/employee/full_name')`

This will import the absence records for the employee who has the Employee Code of **A1234**, and will return just the _absence reason_, _description_, and employee _full name_ into a sheet.

---

##### How do you specify the fields?

If you look at the example above, with the query to the employee absence file.  Whenever you query the _absences_ module, the raw JSON data is returned to you in the background as follows:

```
{
    "id": 10863,
    "absence_reason": "Annual Leave",
    "description": "Family Holiday",
    "absence_date": "2019-10-30",
    "return_date": "2019-11-20",
    "duration": 15.0,
    "units": "Days",
    "physician": "",
    "certificate_number": "",
    "absence_status": "Approved",
    "comments": "",
    "employee": {
      "code": "HOOPS",
      "first_names": "Steve",
      "last_name": "Hooper",
      "full_name": "Steve Hooper",
      "department": "Finance & Accounting"
    },
    "attachments": [
      {
        "description": "sick_certificate.png",
        "url": "_5jGmaQ9uOK3kldP6MU43Q/attachments/vckliR3aopdsUg9LhIljYA/sick_certificate.png",
        "size": 6020
      }
    ]
  }
```

If you notice, there is all the absence data, and then there are 'hierarchical' entries for the employee, and any attachments against this absence entry.

To get to any of the 'top level' data fields, you just need to specify them with a leading forward slash '/' character, e.g.
* /absence_reason
* /duration
* /return_date

But to get to any of the fields under the 'employee' embedded object, you need to specify `/employee/` before the field name, e.g.:
* /employee/code
* /employee/full_name
* /employee/department

Our [API Documentation](https://developer.hrpartner.io) shows you the format of all returned data, so you can work out which fields you need and how to specify them.

---

### DateHRPartner()

Anytime a date is returned from HR Partner, it will be as a string in the `yyyy-mm-dd` format, and if it is a date/time field, it will be returned as a string in the `yyyy-mm-ddThh:nn:ss` format.  These strings look fine in Google Sheets, however they cannot be used to do any date calculations (e.g. adding on days or picking out the month etc.).

For this reason, we provide a function to convert the returned string to a valid Date() field that Google Sheets can use to perform calculations.

Lets assume that you have run a query that returns the employee's date of birth into cell `C3` in `Sheet1`.  And lets also assume that you want the proper date in cell `C10`.  Well, all you need to do is to go to cell C10 in your sheet, and enter the following formula:

`=DateHRPartner(Sheet1!C3)`

This will populate cell C10 with a date object representing the string date in C3.  Now you can do all sorts of DATE() maths on it using the standard Google Sheets functions.

## Support

We don't normally provide in depth support for API connectivity, mainly because customer needs for API data is usually very complex and varied.  We can provide advice and guidance under certain circumstances though, but please be aware that because usually these queries have to be escalated to our development team, that it may sometimes take one or two days for a response.

You can ask any questions about this script, or about our API by sending an email to support@hrpartner.io

## Disclaimer

This script is provided 'as is'.  HR Partner Software Pty Ltd takes no responsibility for anything that may go wrong or break as a result of you downloading, installing or modifying this script.  Your usage of this script or our API denotes your understanding of this, and by doing so, you absolve HR Partner Software Pty Ltd and it's employees and contractors from any fault if anything goes wrong.

