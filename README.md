outdated

# Web-Scraping-Google-Sheets
Web Scraping using Google Sheets
Source: https://thisdavej.com/web-scraping-with-google-sheets-the-definitive-guide/

IMPORTHTML
- Imports data from a table or list within an HTML page.
https://support.google.com/docs/answer/3093339?hl=en

IMPORTXML
- Imports data from any of various structured data types including XML, HTML, CSV, TSV, and RSS and ATOM XML feeds.
https://support.google.com/docs/answer/3093342?hl=en

IMPORTXML
- Imports data from any of various structured data types including XML, HTML, CSV, TSV, and RSS and ATOM XML feeds.
https://support.google.com/docs/answer/3093342?hl=en

IMPORTDATA
- Imports data at a given url in .csv (comma-separated value) or .tsv (tab-separated value) format.
https://support.google.com/docs/answer/3093335



Create Custom Functions for Maximum Flexibility
We can create our own custom functions in Google Sheets to scrape web pages and get precisely the data of interest for a given web page.

Example 1: Get current gasoline prices
First, go to Tools > Script editor using the Google Sheets menu.
Next, enter the following JavaScript code in the code editor that appears:

// Code begins here...
function GAS_PRICE(state) {
  state = state.trim();
  const url = "https://www.gasbuddy.com/USA";
  var response = UrlFetchApp.fetch(url);
  const s = response.getContentText();
  const re = new RegExp(
    '<a id="' +
      state +
      '"[\\s\\S]+?<div class="col-sm-2 col-xs-3 text-right">[\\s]+([\\d.]+)',
    "i"
  );

  var match = re.exec(s);
  var price = "not found";
  if (match != null) {
    price = match[1];
  }
  return price;
}
// Code ends here...


In this function, we:

- Utilize the built-in UrlFetchApp JavaScript function to invoke the URL and fetch the raw content text.
- Leverage regular expressions (regex) to extract the exact text of interest from the content returned.
  - Regular expressions are a very useful tool for web scraping because they allow us to precisely define a search pattern and include the text of interest to capture in parentheses. For more information see this tutorial. I also recommend https://regexr.com/ as an excellent tool to learn, build, and test regular expressions.
  - Our regular expression search pattern includes the value of the "state" parameter supplied to the JavaScript function. The "state" parameter will be one of 50 possible values representing the states in the USA. For example, "CA" = "California". This regex search pattern ensures we retrieve the gas price for just one state since all 50 states are included on the Gas Buddy page.
- Return the gas price retrieved from the regular expression which is contained between the left and right parentheses in the regex pattern.

Using the function.
A2 = CA
=GAS_PRICE(A2)

Returns the gas price in the state of CA fetched from a website.



Example 2: Get the birthdates and life spans of famous people

// Code begins here...
function BIRTHDATE(name) {
  const url = "https://en.wikipedia.org/wiki/" + name;
  var response = UrlFetchApp.fetch(url);
  const s = response.getContentText();

  var match = /<span class="bday">(.+?)<\/span>/.exec(s);
  var bday = "not found";
  if (match != null) {
    bday = ParseDate(match[1]);
  }

  var matchd = /Died<\/th><td>.+?<span style="display:none">\((.+?)\)<\/span> \(aged&#160;/.exec(
    s
  );
  var dday = bday === "not found" ? "not found" : "still alive";
  if (matchd != null) {
    dday = ParseDate(matchd[1]);
  }

  return [[bday, dday]];
}

function ParseDate(s) {
  // Assume invalid date until proven otherwise
  var result = "invalid date";
  var parts = s.split("-");
  if (parts.length === 3) {
    var dt = new Date(parts[0], parts[1] - 1, parts[2]);
    if (dt instanceof Date && isFinite(dt)) {
      result = dt;
    }
  }
  return result;
}
// Code ends here...


Using the function.
A2 = Isaac Newton
=BIRTHDATE(A2)

Returns the life span of Isaac Newton fetched from a website.
