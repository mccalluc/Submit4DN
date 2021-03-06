#Submitting Metadata and Data files to the 4DN-DCIC


In order to make your data accessible, searchable and assessable you should submit as much metadata as possible to the 4DN system along with the raw you have generated. This guide will show you how to find out what kind of metadata we collect for your particular type of experiment and the mechanisms by which you can submit your metadata and data to the 4DN system.

For an overview of the metadata structure and relationships between different items please see [these slides](https://drive.google.com/file/d/0B6cuZNSltxB9RERJNl81ZTRVUlE/view?usp=sharing).

###A note on Experiments and Replicate Sets

The 4DN Consortium is strongly encouraging that chromatin conformation capture genomic experiments be performed using at least two different preparations of the same source biomaterial - i.e. bioreplicates.  In terms of submitting metadata you would submit two Experiments that used the same Biosource, but have different Biosamples. In many cases the only difference between Biosamples may be the dates at which the cell culture or tissue was harvested.  The experimental techniques and parameters will be shared by all experiments of the same bioreplicate set.

You may also have multiple sequencing runs performed at different times using a library prepared from the same Biosample and the same methods up until the sample is sent to the sequencer - i.e. technical replicates.

The replicate information is stored and represented as a set of experiments that includes labels indicating the replicate type and replicate number of each experiement in the set.

The mechanism that you use to submit your metadata will dictate the type of item that you will associate replicate information with, though in the database the information will always end up directly associated with ExperimentSetReplicate objects.  Specific details on formatting information regarding replicates is given below for the [Excel Work Book](#excel_reps) submission.  When submitting using the REST API you should format your json according to the specifications in the schema as described [below](#rest).


#Getting added as a user and a data submitter for a lab
Before you can submit data to the 4DN system you must be a registered user of the site and have the appropriate access credentials. You must be designated as a submitter for the lab for which you want to submit files and metadata. To get set up with an account with the correct access contact the data wranglers at <support@4dnucleome.org>. To validate your credentials, please also cc your PI. We can also create user accounts for lab members who will not submit data but will be able to view submitted data.


**A note on metadata and data accessibility.**
 For most metadata items the default permission will be that the data will only be viewable by the members of the submitting lab and will only be editable by users who have been designated as submitters for that lab. The metadata will also be accessible to data wranglers who can help you review the data and alert you to any issues as the submission is ongoing. Once the data and metadata are complete and quality controlled, they will be released according to the data release policy adopted by the 4DN network.


*A note on the test deployment:* We are deploying the 4DN Data Portal at <https://data.4dnucleome.org>. But at the moment, please use the test portal accessible at <https://testportal.4dnucleome.org>. Data submitted to the test portal may be deleted when server redeployments are necessary; however the forms you prepare can be used for submission to our production portal later.




#Submission of metadata using a Microsoft Excel WorkBook as a template
##Overview
Metadata and data can be submitted to our platform using Microsoft Excel WorkBooks which describe related items in separate sheets. This section provides detailed information on how to fill the WorkBooks. You can check out the [example WorkBook](https://github.com/hms-dbmi/Submit4DN/blob/master/Data_Files/Rao_et_al_2014/fieldsRao.xls?raw=true) we prepared for the data from Rao et. al. 2014 to familiarize yourself with the general structure.


Based on the type of experiment(s) for which you plan to submit data, the data wranglers can provide you with an Excel WorkBook containing several WorkSheets.  Each sheet corresponds to an Item type in our metadata database. The workbook provided should contain all the sheets that you may need for your submission. You can refer to [this table](schema_info.md) for information on all the Item types available in the database.  Each sheet should also contain all the data fields that can be submitted for that Item type. Depending on if you have submitted data before or if you are using shared reagents that have been submitted by other labs, you may not need to provide information on every sheet or in every field.

Generally, it makes sense to begin with the left most sheet in the workbook as the sheets in a workbook are ordered so that Items that have fields that take a reference to another Item as their value appear ‘after’ i.e. to the right of that Item’s sheet in the workbook.

A sheet for an Item starts with a row of field names. *Absolutely required fields are marked with a leading asterick (eg. \*experiment_type).* The second row of the sheet indicates the type of the information expected for the fields. The third row includes a description of each of the fields. In some cases the values that you can submit for a particular field are constrained to a specific set of terms and when this is the case the possible values are shown in the fourth row.

#####Excel Headers

1. Field name
2. Field type (string, number, array, embedded object)
3. Description
4. Additional info (comments and choices for fields with controlled vocabulary)

Any row that starts with “#” in the first column will be ignored, so you can add non-data rows for your own use but **PLEASE NOTE THAT THE FIRST 2 ROWS OF A SHEET SHOULD NOT BE MODIFIED.**

You may notice that in some sheets there are additional commented rows that contain data values. These are rows corresponding to items that already exist in the database and can provide you with identifiers that you can reuse in your submission (see [Referencing existing items](#existing-items) below.  Only those items that either are associated with your lab or are already released to the public will appear in these commented data rows.   Your data entry should begin at the first non-commented row.

##<a name="values"></a>Preparing your workbook for metadata submission
A field can be one of a few different types; string, number/integer, array/list or Item and the type will be indicated in the second row.

Most field values are strings - either a term with controlled vocabulary, i.e. from a constrained list of choices, a string that identifies an Item, or a text description. If the field type is an array, you may enter multiple values separated by commas.

**Basic field formats**
![Basic fields](./field_types.png)

**<a name="basic-field"></a>Basic fields example**
![Basic fields examples](./basic_field_eg.png)
There are some fields values that require specific formatting. These cases and how to identify them are described below.

####When the string must conform to a certain format
In some cases a field value must be formatted in a certain way or the Item will fail validation. In most cases tips on formatting requirements will be included in the *Additional Info* row of the spreadsheet. Examples of these are _Date_ fields (YYYY-MM-DD format) and _URLs_ (checked for proper URI syntax).

In other cases a field value must match a certain pattern. For example, if a field requires a DNA sequence then the submitted value must contain only the characters A, T, G, C or N.


_Database Cross Reference (DBxref) fields_, which contain identifiers that refer to external databases, are another case requiring special formatting. In many cases the values of these fields need to be in database\_name:ID format. For example, an SRA experiment identifier would need to be submitted in the form ‘SRA:SRX1234567’ (see also [Basic fields example](#basic-field) above). Note that in a few cases where the field takes only identifiers for one or two specific databases the ID alone can be entered - for example, when entering gene symbols in the *'targeted\_genes’* field of the Target Item you can enter only the gene symbols i.e. PARK2, DLG1.

####When a field specifies a linked item
Some fields in a Sheet for an Item may contain references to another Item. These may be of the same type or different types. Examples of this type of field include the *‘biosource’* field in Biosample or the *‘files’* field in the ExperimentHiC. Note that the latter is also an example of a list field that can take multiple values.


You can reference an item in the excel workbooks using one of four possible ways: UUID, accession, lab-specific alias, or item-type-specific identifier. More information about these four identifiers is provided in the [Using aliases](#using-aliases) section below.


####When field(s) indicate an embedded object
Some Items can contain embedded sub-objects that are stored under a single Item field name but that contain multiple sub-fields that remain grouped together. These are indicated in the Item spreadsheet using a ‘.’ (dot) notation. For example the *"experiment_relations"* field has 2 sub-fields called *"relationship_type"*, and *"experiment"*. In the spreadsheet field names you will see *experiment\_relations.relationship_type* and *experiment\_relations.experiment*.


If the Item field is designed to store a list of embedded sub-objects, you can enter multiple sub-objects by manually creating new columns and appending incremented integers to the fields names for each new sub-object.


For example, to submit a total of three related experiments to an ExperimentHiC Item you would find the *experimen\_relations.relationship\_type* and *experiment\_relations.experiment* columns, copy them and have total of 6 columns named:


    experiment_relations.relationship_type
    experiment_relations.experiment
    experiment_relations.relationship_type-1
    experiment_relations.experiment-1
    experiment_relations.relationship_type-2
    experiment_relations.experiment-2


and enter a valid *relationship\_type* term and *experiment* identifier to each of the three pairs of columns.

**Multiple linked columns for lists of embedded objects**
![Embedded fields](./embedded_objects.png)

#### <a name="using-aliases"></a>Using aliases
An **alias** is a lab specific identifier that you can assign to an item. Aliases take the form of *lab:id\_string* eg. ```parklab:myalias```. An alias must be unique within all items. Once you submit an alias for an Item then that alias can be used as an identifier for that Item in the current submission as well as in any subsequent submission.

You don't need to use an alias if you are referencing an item that already exists in the database.

####<a name="existing-items"></a>Referencing existing items
Every item in our database is assigned a “uuid” upon its creation, e.g. “44d3cdd1-a842-408e-9a60-7afadca11575”. Items from some item types (Files, Experiments, Biosamples, Biosources, Individuals...) also get a shorter “accession” assigned, e.g. 4DNEX4723419. These two are the default identifying terms of any item. Besides these two, there can also be object specific identifying terms, like award number for awards, or lab name for labs.


| Object | Field | Example | Example (simple)|
|---|---|---|---|
| Lab | name | /labs/peter-park-lab/ | peter-park-lab |
| Award | number | /awards/ODO1234567-01/ | ODO1234567-01 |
| User | email | /users/test@test.com/ | test@test.com |
| Vendor | name | /vendors/fermentas/ | fermentas |
| Enzyme | name | /enzymes/HindIII/ | HindIII |
| Construct | name | /constructs/GFP-H1B/ | GFP-H1B


These three types of values can be used for referencing items in the excel sheets for items that already exist in the 4DN database.


The DCIC has already created all the labs and awards that are part of the 4DN consortium. There will also be other items, for example vendors, enzymes and biosources that will already exist in the database and can be reused in your submissions.  If there is an existing vendor (e.g. new england biolabs) for the new enzyme you are creating, you should reference the existing one instead of creating a new one.

In some cases information for existing items will be present in the Excel Work Sheets provided for your submission.  You can also check the existing items from *collection* pages that list all of them.   The links for item lists can be constructed by ```https://data.4dnucleome.org/ + plural object name``` and the identifiers that can be used for collections are referenced in [this table](schema_info.md).

####<a name="supp_files"></a>Submitting supplementary metadata files
To submit supplementary metadata files, such as pdfs or images, use the **Image** or **Document** schemas, and include the path of the files in the _*attachment*_ column.
The path should be the full path to the supplementary file.

####<a name="excel_reps"></a>Replicate information submitted with Experiments
All experiments must be part of a replicate set - even if it is a set containing only a single experiment.  When preparing your submission you should determine how many replicate sets you will be submitting and create an entry - with an alias and preferably an informative description - for each set in the ExperimentSetReplicate sheet.

![ExperimentSetReplicate example](./repsets_w_desc.png)


Then when entering information about individual experiments on the specific Experiment_ sheet you should enter the alias for the replicate set to which the experiment belongs and indicate the bioreplicate and technical replicate number for that experiment. In the example below the replicate set consists of five experiments categorized into one of two bioreplicates - bio_rep_no 1 and bio_rep_no_2, each of which contains three and two technical replicates, respectively.

![Experiments with replicate info example](./expts_w_rep_info.png)



##Submitting Excel Workbooks to 4DN-DCIC
The 4DN DCIC website has an REST API for fetching and submitting data. In our **Submit4DN** package the ```import_data``` script utilizes an organized bundle of REST API commands that parse the Excel workbook and submit the metadata to the database for you. The ```get_field_info``` script that is also part of the package can be used to generate the Excel workbook templates used for submission for all or a selected set of worksheets.
The package can be installed from pypi.

####Installing the package
The Submit4DN package is registered with Pypi so installation is as simple as:


    pip3 install submit4dn


####Source code
The source code for the submission scripts is available on [github](https://github.com/4dn-dcic/Submit4DN).


Note if you are attempting to run the scripts in the wranglertools directory without installing the package, then in order to get the correct sys.path you need to run the scripts from the parent directory as modules using the -m flag.

```python3 -m wranglertools.import_data  filename.xls```


###<a name="access"></a>Establishing a Connection to the 4DN-DCIC servers
An access key and a secret key are required to establish a connection to the 4DN database and to fetch, upload (post), or change (patch) data. Please follow these steps to get your keys.

1. Log in to the 4DN website with your username (email) and password.
    - test server: <https://testportal.4dnucleome.org>
    - production server: <https://data.4dnucleome.org>

    Note that we are using the [Oauth](https://oauth.net/) authentication system which will allow you to login with a google or github login.  _The email associated with the account you use for login must be the same as the one registered with the 4DN-DCIC._

2. Once logged in, go to your ”Profile” page by clicking **Account** on the upper right side of the page.  In your profile page, click the green “Add Access Key” button, and copy the “access key ID” and “secret access key” values from the pop-up page. _Note that once the pop-up page disappears you will not be able to see the secret access key value._ However, if you forget or lose your secret key you can always delete and add new access keys from your profile page at any time.


3. Once you have your access key information, create a file in your home directory named “keypairs.json”. This file will contain your key information in json format and will be read by the Submit4DN scripts to establish a secure connection. The contents of the file must be formatted as shown below - replace key and secret with your new “Access Key ID” and “Secret Access Key”.

**Sample content for keypairs.json**

```json
{
  "default": {
    "key": "ABCDEFG",
    "secret": "abcdefabcd1ab",
    "server": "https://testportal.4dnucleome.org/"
  }
}
```

**Tip:** If you don’t want to use that filename or keep the file in your home directory you can use the --keyfile parameter as an argument to any of the scripts to provide the path to your keypairs file.
If in the file, the key is not called “default” you can use the --key parameter to indicate the stored key name.

    import_data --keyfile Path/name_of_file.json --key NotDefault

###Uploading metadata with import_data
You can use `import_data` either to upload new items or to modify metadata fields of existing items. This script will accept the excel workbook you prepared, and will upload every new item in the sheets.  This script is also used to upload data files to the 4DN data store - this is done in a separate step after your File metadata has been successfully uploaded.


####Testing your metadata (_WARNING Not yet implemented_)
When you run the import_data  script on your metadata excel workbook without any arguments the system will test your data for compatibility with our metadata structure and report back to you any problems (in effect a dry run). The metadata will not be submitted, so you can take advantage of this feature to test your excel workbook.

```import_data My_metadata.xls``` (Not yet implemented)


####Uploading (posting) & Modifying (patching) Metadata
When you submit your metadata, if a row in any sheet corresponds to a new item that has not previously been submitted to the 4DN database you will be POSTing that data via the REST API. Most of your entries in the first submission will be POSTs. To activate posting you need to include the ```--update``` argument to ```import_data```.


	import_data My_metadata.xls --update


If you need to modify an existing item, you can use the patch function. To be able to match your item to the one on the server, a pre-existing identifier must be used in the spreadsheet. If you included an alias when you posted the item, you can use this alias to reference the existing item in the database -- uuids, @ids, or accessions can also be used to [reference existing items](#existing-items) in the database. When you run ```import_data``` and an existing entry is encountered, the script will prompt you ‘Do you wish to PATCH this item?’. You will be prompted for every existing item that is found in your workbook. The ```--patchall``` argument will allow automatic patching of each existing item, bypassing the prompts.

	import_data My_metadata.xls --patchall

####When your upload is aborted
If for some reason the script fails in the middle of the upload process or errors are encountered for certain items, some items will have been posted while others will have not. When you fix the problem that caused the process to terminate, you can rerun the script using both the ```--patchall``` and ```--update``` arguments. Those items that had already been posted will be ‘patched’ using the data in the sheet and the items that had not been posted yet will be loaded.

	import_data My_metadata.xls --patchall --update

Please note that a delete feature is not yet implemented. Neither items nor fields in items can be deleted; they can only be patched, i.e. overwritten.

###Uploading files with import_data
The 4DN databased distinguishes two main categories of files: (1) files that support the metadata, such as Documents or Images, and (2) data files for which metadata is gathered and are specified in specific File items/sheets (eg. FileFastq).


The first category can be uploaded along with the metadata by using the “attachment” fields in the excel workbook (eg. pdf, png, doc, …) as [described previously](#supp_files).


The second category includes the data files that are the results of experiments, eg. fastq files from HiC experiments. These data files are bound to a File item with a specific type eg, FileFastq that contains relevant metadata about the specific result file. Metadata for a file should be submitted as part of your experiment metadata submission as described above. The actual file upload to the 4DN file store in the cloud will happen in a subsequent submission step. **NOTE that the filename is not part of the initial File metadata submission.** This second step will be triggered by a successful metadata submission that passes review by the 4DN DCIC.


####File upload
To upload your files, use the file submission excel sheet provided, and copy paste all your file (FileFastq) aliases from your metadata excel sheet to the aliases field of the file submission sheet. Under filename enter the full paths to your files. Once completed use import_data with the ```--patchall``` argument to start upload. The DCIC automatically checks file md5sums to confirm successful upload and to ensure that there are no duplicate files in the database.


To replace a file that has already been uploaded to 4DN - that is to associate a different file with existing metadata, associate the file path for the new file with an existing alias. **NOTE that every time you patch with a filename (even if it is the same filename) the file will be uploaded. Please use care when including a filename in your File metadata to avoid unnecessary uploads.** We plan to avoid this issue in future releases by pre-checking md5sums.


##Generate your own Excel Workbook with get\_field\_info
To create the data submission xls forms, you can use get\_field\_info, which is part of the Submit4DN package.

The scripts accepts the following parameters:.


    --keyfile      access key file path (default is “home_folder/keypairs.json”)
    --key           name of the key (default is “default”)
    --type          use for each sheet that you want to add to the excel workbook
    --descriptions  adds the descriptions in the second line (by default True)
    --enums         adds the enum options in the third line (by default True)
    --comments   adds the comments together with enums (by default False)
    --writexls        creates the xls file (by default True)
    --outfile          change the default file name "fields.xls" to a specified one


**Examples generating a single sheet:**

    get_field_info --type Biosample
    get_field_info --type Biosample --comments
    get_field_info --type Biosample --comments --outfile biosample.xls


**To get the complete list of relevant sheets in one workbook:**

    get_field_info --type Publication --type Document --type Vendor --type Protocol --type BiosampleCellCulture --type Biosource --type Enzyme --type Construct --type TreatmentChemical --type TreatmentRnai --type Modification --type Biosample --type FileFastq --type FileSet --type IndividualHuman --type IndividualMouse --type ExperimentHiC --type ExperimentCaptureC --type ExperimentRepliseq --type Target --type GenomicRegion --type ExperimentSet --type ExperimentSetReplicate --type Image --comments --outfile AllItems.xls

##<a name="rest"></a>Submission of metadata using the 4DN REST API
The 4DN-DCIC metadata database can be accessed using a Hypertext-Transfer-Protocol-(HTTP)-based, Representational-state-transfer (RESTful) application programming interface (API) - aka the REST API.  In fact, this API is used by the ```import_data``` script used to submit metadata entered into excel spreadsheets as described [in this document](https://docs.google.com/document/d/1Xh4GxapJxWXCbCaSqKwUd9a2wTiXmfQByzP0P8q5rnE). This API was developed by the [ENCODE][encode] project so if you have experience retrieving data from or submitting data to ENCODE use of the 4DN-DCIC API should be familiar to you.   The REST API can be used both for data submission and data retrieval, typically using scripts written in your language of choice.  Data objects exchanged with the server conform to the standard JavaScript Object Notation (JSON) format.  Libraries written for use with your chosen language are typically used for the network connection, data transfer, and parsing of data  (for example, requests and json, respectively for Python).  For a good introduction to scripting data retrieval (using GET requests) you can refer to [this page](https://www.encodeproject.org/help/rest-api/) on the [ENCODE][encode] web site that also has a good introduction to viewing and understanding JSON formatted data.

[encode]: https://www.encodeproject.org/

###Connecting to the server
Your script will need to use an access key and secret that you can obtain by following [these instructions](./excel_submission.md#access) to connect with either the test or production server.  Exactly how you format and pass the connection information to the server depends on your scripting language and the libraries that you use with it.

**Base URLs for submitting and fetching data are:**
*Test Server:* <https://testportal.4dnucleome.org/>
*Production Server:* <https://data.4dnucleome.org/>

You can refer to the ```FDN_key``` and ```FDN_connection``` classes in [the ```get_field_info.py``` library](https://github.com/4dn-dcic/Submit4DN/blob/master/wranglertools/get_field_info.py) in Submit4DN for an example of how to generate the necessary information that will be passed to the server with each request.

###Identifying specific items
Your script will need to add a uniquely identifying token to the Base URL in order to GET, POST or PATCH metadata for that item. IDs that can be used include: uuids, accessions, certain type specific identifiers and aliases.  See the sections on ['Using Aliases' and 'Referencing existing items'](./excel_submission.md#using-aliases) for the types of identifiers that can be used in your requests.

###Ordering of POST requests
Because in many cases fields in one Item may refer to another Item eg. the ```biosample``` field in the ```experiment_hi_c``` schema it is necessary to POST the referenced item prior to POSTing the item that refers to it.  A sensible POSTing order is specified in the ```sheet_order``` array located around line 228 in the [```get_field_info.py``` library](https://github.com/4dn-dcic/Submit4DN/blob/master/wranglertools/get_field_info.py) library.

###json formatting
The most important component of your submission is the proper formatting of the data into json so it will map correctly onto the 4DN metadata schemas.  The details of the schemas for all object types in the database can be viewed at <https://data.4dnucleome.org/profiles/>.  Individual schemas can be viewed and/or retrieved via GET by appending the schema file name to the above URL (eg. for the Hi-C experiment schema <https://data.4dnucleome.org/profiles/experiment_hi_c.json>). For a listing of all schema files and associated resource names see [this table](schema_info.md), which is up to date with the current schemas in the database.

Depending on the Item type that you are submitting there may be fields that require values (eg. *experiment_type* for experiments), fields for which values should never be submitted (eg. ‘date_created’ as this is an automatically generated value) and fields with specific formatting and fields that accept values of specific types.  In many cases the values must be selected from a list of constrained choices.  The documentation on field values [described in detail above](#values) and the annotated json document below can be used as a guide on formatting your json.  In addition, the unordered and unfiltered excel workbooks produced by ```get_field_info``` can be a useful reference for determining exactly what fields are associated with what object types.  The processed workbook that is actually used for data submission does not reflect the exact schema structure **should not** be used as a direct reference for API submission.


**json for an ExperimentHiC POST request**

```
{
    "lab": "4dn-dcic-lab",
    "award": "/awards/OD008540-01/",
    "aliases": ["dcic:myalias"]
    "experiment_type": "in situ Hi-C",
    "biosample":"4DNBS1NUPXMH",
    "biosample_quantity": 2000000,
    "biosample_quantity_units": "cells",
    "dbxrefs": ["SRA:SRX764985", "GEO:GSM1551599"],
    "description": "A useful description of this experiment",
    "documents": ["dcf15d5e-40aa-43bc-b81c-32c70c9afb01"],
    "experiment_relation":[{"relationship_type":"controlled by","experiment":"4DNEX067APU1"}],
    "experiment_sets":["431106bc-8535-4448-903e-854af460b260"],
    "files": ["4DNFI067APU1", "4DNFI067APU2", "4DNFI067APA1"],
    "filesets": ["4d6ecff9-7c67-4096-bca8-515246767feb"],
    "follows_sop": "Yes",
    "crosslinking_method": "1% Formaldehyde",
    "crosslinking_time": 30,
    "crosslinking_temperature": 37,
    "digestion_enzyme": "MboI",
    "enzyme_lot_number": "123456",
    "digestion_temperature": 37,
    "digestion_time": 30,
    "ligation_time": 30,
    "ligation_temperature": 37,
    "ligation_volume": 1,
    "tagging_method": "Biotinylated ATP",
    "fragmentation_method": "chemical",
    "average_fragment_size": 100,
    "notes": "sample dcic notes",
    "protocol": "131106bc-8535-4448-903e-854af460b244"
}
```

**Field specification - how to find out what fields need what format, when to provide values and other useful tips**

The three fields below are common to every Item - you don't always need to include aliases but they will make some things a lot easier.

```
    "lab": "4dn-dcic-lab",  # required for POST with value of an existing lab identifier - this is an alias
    "award": "/awards/OD008540-01/", # required for POST with value of an existing award identifier - this a specific item type identifier
    "aliases": ["dcic:myalias", "dcic:myotheralias"], # POST or PATCH an array with values that can be used as identifiers
```
Accession and uuid are automatically assigned during initial posting so can only be used as identifiers to PATCH existing Items - not for POSTing.  Note that all item types have a uuid but not all items have accessions.

```
    "accession":"4DNEX067APU1", # PATCH ONLY can be used as an identifier - automatically assigned on POST
    "uuid": "75041e2f-3e43-4388-8bbb-e861f209c444", # PATCH ONLY can be used as an identifier - automatically assigned on POST
```
While we encourage you to submit as many fields as possible there are some fields that are absolutely required to post an item.  These required fields are identified in the "required" field of the schema.

```
    "required": [
        "experiment_type",
        "award",
        "lab",
        "biosample"
    ],
```
If they are left out of a POST request will cause an error.  You don't need to include required fields if you are PATCHing an existing Item.

```
    "experiment_type": "in situ Hi-C", # required for POST string field
    "biosample":"231111bc-8535-4448-903e-854af460b254", # required for POST with value of an alias used in Biosample sheet or previously existing biosample identifier
```
There are number fields and string fields - the field type can be found by referring to the schema directly or the workbook templates produced by ```get_field_info```.

```
   "biosample_quantity_units": {
        "title": "Quantity Units",
        "description": "The units that go along with the biological sample quantity",
        "enum": [
            "g",
            "mg",
            "μg",
            "ml",
            "cells"
        ],
        "type": "string"
    },
    "biosample_quantity": {
        "title": "Biological Sample Quantity",
        "description": "The amount of starting Biological sample going into the experiment",
        "type": "number"
    },
```

Some of the fields will only accept values from a constrained set of choices.  These are indicated by 'enum' lists in the schemas (see above) or the 'Choices' list in the Excel workbooks.  Some fields also have dependencies in that if one field has a value then another field must also have a value.  Dependencies are specified in the schema.

```
    "dependencies": {
      "biosample_quantity": ["biosample_quantity_units"],
      "biosample_quantity_units": ["biosample_quantity"]
    },
```

```
    "biosample_quantity": 2000000, # number field
    "biosample_quantity_units": "cells", # string field - possible values from enum list
```
Some fields accept an array of string values.

```
    "dbxrefs": {
        "description": "Unique identifiers from external resources.",
        "type": "array",
        "items": {
            "title": "External identifier",
            "type": "string",
        }
```
 and must be enclosed in square braces [].  The braces are required even if only a single value is being submitted for that field.  The strings may be literal values for the fields or references to other objects, for example by UUID as in the "documents" field below and the acceptable value type can be determined by looking at the "items" stanza of the field.

```
    "dbxrefs": ["SRA:SRX764985", "GEO:GSM1551599"], # string - array with values of specific format db_prefix:accession
    "documents": ["dcf15d5e-40aa-43bc-b81c-32c70c9afb01"], # uuid to existing document as identifer
```

Some fields contain lists of sub fields that remain linked - effectively lists of embedded objects.

```
"experiment_relation": {
    "title": "Experiment relations",
    "description": "All related experiments",
    "type": "array",
    "items": {
        "title": "Experiment relation",
        "type": "object",
        "additionalProperties": false,
        "properties": {
            "relationship_type": {
                "type": "string",
                "description": "A controlled term specifying the relationship between experiments.",
                "title": "Relationship Type",
                "enum": [
                    "controlled by",
                    "control for",
                    "derived from",
                    "source for"
                ]
            },
            "experiment": {
                "type": "string",
                "description": "The related experiment",
                "linkTo": "Experiment"
            }
        }
    }
}
```
The embedded objects are enclosed in curly braces {} and as usual objects in the list are comma separated and enclosed in square brackets [].

```
    "experiment_relation":[{"relationship_type":"controlled by","experiment":"4DNEX067APU1"}, {"relationship_type":"derived from","experiment":"4DNEX067APV1"}], # array of embedded objects - one experiment_relation object with 2 linked fields
```

When a field references another object the value you provide can be any identifier for that object eg. uuid, accession or type specific identifiers.  If you are not sure if a field is referencing another object look for the 'linkTo' tag in the field specification in the schema.

```
    "protocol": {
        "title": "Reference Protocol",
        "description": "Reference Protocol Document.",
        "type": "string",
        "linkTo": "Protocol"
    },
```

The identifiers that you can use can be found in the "identifyingProperties" field of the schema of that Item type.

*Protocol identifyingProperties*
```
"identifyingProperties": ["uuid", "aliases"],
```

*protocol field in experiment_hi_c submission*
```
"protocol": "131106bc-8535-4448-903e-854af460b244"
```

Finally there are some fields that cannot or should not be submitted.  Many of these are marked with ```"permission": "import-items"``` and ```"readonly":true```.

```
"accession": {
    "type": "string",
    ...
    "comment": "Only admins are allowed to set or update this value.",
    "readonly": true,
    "permission": "import_items"
},
```

Others are marked as calculated properties that are derived from other existing information.

```
"experiment_summary": {
    "type": "string",
    "calculatedProperty": true,
    ...
},
```
 Trying to submit protected or calculated fields will result in an error.
