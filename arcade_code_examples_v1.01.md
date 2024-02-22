- [Markdown Template - Arcade Examples](#markdown-template---arcade-examples)
  - [Code and Syntax Highlighting](#code-and-syntax-highlighting)
  - [Code and Syntax Highlighting - Microsoft Teams-Compatible](#code-and-syntax-highlighting---microsoft-teams-compatible)
    - [Inline block of code](#inline-block-of-code)
- [Date Functions](#date-functions)
  - [Symbolize Features Dynamically by Date Categories Using DateDiff(())](#symbolize-features-dynamically-by-date-categories-using-datediff)
  - [Symbolize Features Dynamically by Date](#symbolize-features-dynamically-by-date)
- [Geometry Functions](#geometry-functions)
  - [Calculate Area Dynamically](#calculate-area-dynamically)
- [Logical Functions](#logical-functions)
  - [Symbolize Features by Presence/Absence of an Attribute Value with IsEmpty](#symbolize-features-by-presenceabsence-of-an-attribute-value-with-isempty)
- [Pop-Up Examples](#pop-up-examples)
  - [Calculated Fields](#calculated-fields)
  - [FeatureSets: Access Data from Another Layer in a Popup](#featuresets-accessing-data-from-another-layer-in-a-popup)


# Markdown Template - Arcade Examples
This is a section for markdown syntax examples. Preview-able and exportable via [https://dillinger.io](https://dillinger.io/)

## Code and Syntax Highlighting

```javascript
//This is an example javascript code block, to also be used for ESRI's arcade
if ($feature.MATCHSTAT == 'UNMATCHED') {
    return 'UNMATCHED'
    } 
    
    else if ($feature.MATCHSTAT == 'MATCH') {
    return ''
    }
    
    else if ($feature.MATCHSTAT == 'EXEMPT'){
    return 'EXEMPT'
    }
```

```python
#This is an example python code block
s = "Python syntax highlighting"
print s
```

```
No language specified, so no syntax highlighting in markdown.
Let's throw in a <b>tag</b> to give an example.

In VSCode, it shows as a plain text block.
```
## Code and Syntax Highlighting - Microsoft Teams-Compatible

This section tests markdown display in microsoft teams.

Multi-line code block:
```javascript
if ($feature.MATCHSTAT == 'UNMATCHED') {
    return 'UNMATCHED'
    } 
    
    else if ($feature.MATCHSTAT == 'MATCH') {
    return ''
    }
    
    else if ($feature.MATCHSTAT == 'EXEMPT'){
    return 'EXEMPT'
    }
```
### Inline block of code
Inline block of code `var = $feature.layer` example.

# Date Functions

## Symbolize Features Dynamically by Date Categories Using DateDiff(())
This example is from the parcel status application. It uses the `DateDiff(())` [function](https://developers.arcgis.com/arcade/function-reference/date_functions/#datediff).

```javascript
//areas which are updated 6 months ago or less belong in the first group
if (DateDiff(Now(), Date($feature.GISDate), 'days') <= 182) {
     return "Geometry updated within the last 6 months" }
//areas which are updated more than 6 months but less than 12 months ago belong in the second group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') > 182 &&
DateDiff(Now(), Date($feature.GISDate), 'days') <= 365) {
    return "Geometry updated between 6 and 12 months ago" }
//areas which are updated more than 1 year but less than 2 years ago belong in the third group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') > 365 &&
DateDiff(Now(), Date($feature.GISDate), 'days') <= 730) {
    return "Geometry updated between 1 and 2 years ago" }
//areas which are updated more than 2 years but less than 3 years ago belong in the fourth group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') > 730 &&
DateDiff(Now(), Date($feature.GISDate), 'days') < 1095) {
    return "Geometry updated between 2 and 3 years ago" }
//areas which are updated 3 years ago or more belong in the fifth group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') >= 1095) {
     return "Geometry updated more than 3 years ago" }
//areas which do not satisfy any of the given conditions belong in the fourth group
else {
     return "None of these conditions" }
```

Returns a map like this:

![Parcel Status App GIS Recency](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_datediff_example02.jpg)

## Symbolize Features Dynamically by Date
This example is from the parcel status application. It uses an `if else` [logic](https://developers.arcgis.com/arcade/guide/logic/) with variables set by attributes in a single dataset.

```javascript
var GISDate = Date($feature.GISDate);
var SubmittedDate = Date($feature.SubmittedDate);


If (GISDate<SubmittedDate) {
    return "In Publishing Queue"
}
else {
    return "Not In Publishing Queue"
}
```

Returns a map like this:

![Parcel Status App In Publishing Queue](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_date_example_01.jpg)

# Geometry Functions

## Calculate Area Dynamically
This example operates on the standardized parcel dataset and uses the `AreaGeodetic` [geometry function](https://developers.arcgis.com/arcade/function-reference/geometry_functions/#areageodetic) to calculate feature area, taking into account the curvature of the Earth.

```javascript
//Correct return for Dynamically Calculating Acreage Percent Difference
var GLACRES = $feature.ACRESGL;
var GISACRES = AreaGeodetic($feature,'acres');
var AC_Diff_PCT = ((Abs(GLACRES-GISACRES))/(GLACRES+GISACRES/2))*100;
Round(AC_Diff_PCT,2)
```

# Logical Functions

## Symbolize Features by Presence/Absence of an Attribute Value with IsEmpty
This example is from the parcel status application. It uses the `IfEmpty` logical function to classify and symbolize features by an attribute value.

```javascript
var GISspan = $feature.SPAN;
var PROPTYPE = $feature.PROPTYPE;
if (!IsEmpty(GISspan)) {
    return ("MATCHED")
}
else if ((IsEmpty(GISspan)) && (PROPTYPE =='PARCEL')) {
    return ("UNMATCHED")
}
else if (PROPTYPE != 'PARCEL') {
    return ("EXEMPT from GIS Std")
}
```

Returns a map like this:

![Parcel Status App Town Mapping Status](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_IsEmpty_example_01.jpg)

# Pop-Up Examples
## Calculated Fields
This is an example of a popup that displays dynamically calculated fields.

```javascript
var GISACRES = Round(AreaGeodetic($feature,'acres'),2);
var GLACRES = $feature.ACRESGL;
var AC_Diff = Round(Abs(GLACRES-GISACRES),2);
var AC_Diff_PCT = Round((((Abs(GLACRES-GISACRES))/(GLACRES+GISACRES/2))*100),0);

"Parcel with GIS SPAN "+$feature.SPAN+" in "+$feature.TOWN+" has "+$feature.ACRESGL+" acres in the "+$feature.GLYEAR+" annual grand list."+
" This parcel reflects "+GISACRES+" acres as drawn in "+$feature.YEAR+"."+" This is a difference of "+AC_Diff+" acres, or "+AC_DIFF_PCT+"%."
```

Returns a popup like this:

![Popup with dynamic calculated fields](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_popup_example_01.jpg)


## FeatureSets: Access Data from Another Layer in a Popup
This example displays information from the Property Transfer (point) layer if a parcel is clicked and a transfer point falls within (intersects) that parcel. This expression is created under the popup for the parcel layer. It also includes a note for parcels that are multi-SPAN to indicate that multiple properties and owners may exist within a single parcel.

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;
//Reference PTTR layer
var transferLayer = FeatureSetByName($map, "Property Transfers");
var pttrStart = Date(2018, 12, 31);
//Check for parcel PROPTYPE only (no ROWs, water, etc.)
if (parcelFeature.PROPTYPE == "PARCEL") {
  //Look for PTTR points that intersect parcel features
  var transferFeatures = Intersects(transferLayer, parcelFeature); 
  //If a PTTR point intersects a parcel
  if (Count(transferFeatures) > 0) {
    //Checks below for multi=SPAN parcel by looking for unqiue SPANs
    var uniqueSpan = true;
    var firstSpan = null;
    var allRecords = "";
    for (var transfer in transferFeatures) {
      if (transfer.postedDate>pttrStart){
        if (firstSpan == null) {
          firstSpan = transfer.SPAN;
        } else if (firstSpan != transfer.SPAN) {
          uniqueSPAN = false;
        }       
        allRecords += "Closing Date: " + Text(transfer.closeDate, 'YYYY-MM-DD') + "\n";
        allRecords += "Seller: " + transfer.sellEntNam +" "+transfer.sellFstNam+" "+transfer.sellLstNam+ "\n";
        allRecords += "Buyer: " + transfer.buyEntNam +" "+transfer.buyFstNam+" "+transfer.buyLstNam+ "\n\n";
      }
    }
    if (!uniqueSpan) {
      return "NOTE: This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel.\n\n "+allRecords;
    } else if (allRecords != ""){
      return allRecords;
    }
  } else {
  //No record of transfer between 2019-present
    return "There is no record of a property transfer for this parcel between 2019 and present."; 
  }
} else {
//Note for non-parcel features
return "This feature is categorized as "+parcelFeature.PROPTYPE+". Transfer data not applicable."; 
}
```

Note the number of stacked polygons associated with this SPAN in the top right, and the multi-SPAN flag included in the popup:
![Popup showing a multi-SPAN parcel and list of all transfers since 2019](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_popup_featureset_multipart_01.JPG)


If only showing transfers after a certain date (e.g., transfers that have taken place since the current version of the Grand List), amend the date qualifier (line 3) and subsequent output messages where applicable. 

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;
//References PTTR point layer within the map
var transferLayer = FeatureSetByName($map, "Property Transfers");
// Adjust the date as needed per latest Grand List
var GLDate = Date(2022, 4, 1);
//Isolate only PARCEL features from parcel layer (no ROWs, water, etc.)
if (parcelFeature.PROPTYPE == "PARCEL") {
  //Look for any PTTR points that intersect (lie within) with the parcel
  var transferFeatures = Intersects(transferLayer, parcelFeature);
  //If more than 0 PTTR points are within the parcel
  if (Count(transferFeatures) > 0) {
    //Checks below for multi-SPAN parcels by looking for unique SPANs
    var uniqueSpan = true;
    var firstSpan = null;
    var recordsAfterDate = "";
    for (var transfer in transferFeatures) {
      //If the closing date is after the current GL (i.e., has been transferred since annual GL was published)
      if (transfer.closeDate > GLDate) {
        if (firstSPAN == null) {
          firstSpan = transfer.SPAN;
        } else if (firstSpan != transfer.SPAN) {
          //Flag to indicate there are multiple PTTR SPANs within a single parcel (indicates multi-SPAN)
          uniqueSpan = false;
        }
        //Report on survey info for popup
        recordsAfterDate += "Closing Date: " + Text(transfer.closeDate, 'YYYY-MM-DD') + "\n"; 
        recordsAfterDate += "Seller: " + transfer.sellEntNam +" "+transfer.sellFstNam+" "+transfer.sellLstNam+ "\n";
        recordsAfterDate += "Buyer: " + transfer.buyEntNam +" "+transfer.buyFstNam+" "+transfer.buyLstNam+ "\n\n";
      }
    }
    //If there are multiple PTTR SPANs within a single parcel (i.e., it is multi-SPAN)
    if (!uniqueSPAN) {
      return "NOTE: This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel.\n\n" + recordsAfterDate;
      //If there is only one SPAN for the parcel, find all those between the current Grand List and present
    } else if (recordsAfterDate != "") {
      return recordsAfterDate;
      //No transfers between current GL and present
    } else {
      //UPDATE YEAR following annual GL join
      return "There is no record of a property transfer for this parcel since the current statewide Grand List (2022).";
    }
  } else {
    //No transfer of this parcel at all (since 2019)
    return "There is no record of a property transfer for this parcel since the current statewide Grand List (2022).";
  }
} else {
  //Parcel is not a PROPTYPE = PARCEL feature
  return "This feature is categorized as "+parcelFeature.PROPTYPE+". Transfer data not applicable.";
}
```
This popup shows an output for the two above expressions; first all transfers since 2019, followed by only those since the current Grand List:
![Popup showing transfers since 2019 (all available) and transfers since the current Grand List](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_popup_featureset_01.JPG)

For two polygon layers (e.g., surveys associated with parcels), consider the appropriate spatial relationship. An intersection may incorrectly associate a survey with multiple unrelated parcels if the boundary is not drawn perfectly. Using a polygon centroid for the survey should remove commission errors, but may also omit some surveys that should be related to a parcel (e.g., subdivisions where the centroid only falls within one of the new parcels). 

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;
//Reference survey layer
var surveysLayer = FeatureSetByName($map, "Surveys Accessible via Vermont Land Survey Library"); 
// Intersect the parcel with the polygons of the surveys layer
var surveysIntersect = Intersects(parcelFeature, surveysLayer);
//Count number of attachments for a survey record
var cnt = 0 
for (var att in surveysIntersect) {
  cnt += Count(Attachments(att)) 
}
if (Count(surveysIntersect) > 0) {
  var surveyInfo = "";
  for (var survey in surveysIntersect) {
    // Check if the centroid of the survey polygon falls within the clicked parcel
    var surveyCentroid = Centroid(Geometry(survey));
    if (Contains(parcelFeature, surveyCentroid)) { //Report on survey info
      surveyInfo += "Survey Type: " + survey.survey_type + "\n";
      surveyInfo += "Survey Date: " + Text(survey.survey_date, 'YYYY-MM-DD') + "\n";
      surveyInfo += "Surveyor: " + Text(survey.surveyor_name) + "\n\n";
    }
  }
  if (surveyInfo != "") {
      //If there is more than one attachment for a survey:
      if (cnt >1){
      var surveysInfo = surveyInfo + "See link below. Additional survey(s) related to this parcel may be available at https://landsurvey.vermont.gov/"
      return surveysInfo
    }
    else {
      return surveyInfo;
    }
  } else {
    return "Unable to find survey(s) associated with this parcel. Please visit https://landsurvey.vermont.gov/ to verify whether any surveys are available.";
  }
} else {
  return "Unable to find survey(s) associated with this parcel. Please visit https://landsurvey.vermont.gov/ to verify whether any surveys are available.";
}
```

A link to the survey attachment can also be included (note: if there are multiple attachments for a record this expression will only return the first one. A method for listing multiple attachments is still tbd):
```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;
// Retrieve the feature layer containing the attachments
var attachmentLayer = FeatureSetByName($map, "Surveys Accessible via Vermont Land Survey Library");
// Find intersecting features from the attachment layer
var intersectingAtts = Intersects(attachmentLayer, parcelFeature);
// If there are attachments
if (Count(intersectingAtts) >0) {
  for (var survey in intersectingAtts) {
    var surveyCentroid = Centroid(Geometry(survey));
    // If survey polygon centroid falls within parcel, create link for first available attachment
    if (Contains(parcelFeature, surveyCentroid)) {
        var ObjectID = survey.OBJECTID //ID for record
        var AttachID = First(Attachments(survey)).ID //ID for first attachment associated with record
        var Part1 = "https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_Land_Survey_Library_reviewed_v2_1/FeatureServer/0/"
        var Part2 = "/attachments/"
        var link = Part1 + ObjectID + Part2 + AttachID
     }
  }
}
return link
```
This popup shows that there are two surveys whose centroids fall within the selected parcel. The link references the first survey; all others must be accessed via the Survey Library per the note.
![Popup showing info from surveys whose centroid intersect with clicked parcel](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_popup_featureset_attachment_01.JPG)