# DuplicateImageFinder
Goal is to eliminate duplicate images in family photo library - duplicates we caused ourselves.  This is a hobby effort to build a set of tools to identify duplicate images across 25,000 to 50,000 images (or more) using image recognition and other techniques AND deal with image dates.  Image dates are important becasue they help position the photo on a timeline so if desired, you can look at images both forward and backwards in time.  The tool will contain capability to adjust “dates” such as “date taken” and "file date" to try and line up the date taken with file related information to the degree possible.  I will build seperate tools for each main function in the style of old UNIX to ease "getting it right" and having loose coupeling to help prevent cross system bugs from creeping in. My plan is the "data" will be where coupling takes place.  Intend to keep in mind with each design decision we want a framework from the beginning beginning that allows "merger" of tools into a single User Interface or Container.  Since we do not have all problems solved yet with how we will accomplish everything with respect to the user interface I did not want to design that first - and potentially never getting around to making this set of tools work.  I intend to "make it work" as command line centic tools then "polish the elegence" with a user interface.

I also plan in relevant places to allow "call outs" to external tools (such as an image editor) allowing the user to see or work the image in a tool of their choice.  This concept does cause very deep design problems I have not fully worked through becasue the potential exists for adjusting image dates, image editing, as well as creation of yet another file, or a file that contain the original image and a saved edited image and or image edits stored in a sidecar file (for non-destructive image editing).  None of these chznges would be "in th system to detect duplicates" unless the system was re-run (a lengthy process) or unless a feature was added (ToDo: insert image changes into system) to deal with this myrid of issues.

I plan for this system to work only with image files with specific pre-defined file extensions (what ever is supported by image recognition functionality).  ToDo: define list of supported file extensions.

** Plan For Tools **
** (BIISTool) **
Initial image scanning data base creation tool.  The tool will scan designated location for all “image” files and collect information about them.
  1. Allow for user selection of locations to scan
  2. Populate data base after scanning all locations
  3. leverage image recognition tools to create hash totals to use to match similar files

** (DCTool) **
This tool will identify “deletion candidates”.  Deletion candidates are defined by the rules specified by the user from the array of possible data from the prior tool.  
  1. Allow entry (screen based) of deletion candidate selection criteria 
  2. Create python code file to do the candidate selection leveraging SQL. (this may not be best option)
  3. Scan data base using the criteria and create list (stored in the data base) of similar image sets of 2 or more files that match based on “matching rules”
  4. Identify all files that have “no duplicates” and load this data set into a separate table.  This may facilitate movement of these files to a new location and allow deletion from the original location.  This would reduce system run\ scan time and head toward a single unduplicated image library.
  5. Determine which file is the “original” or DupeSetMaster - and assert what all dates\changes need to be based on data in similar files
	  A. Each file will be marked in data base as a likely duplicate in a set (DupeSet) of similar files
	  B. Files in DupeSet are each classified as to deletion priority within the set (a score measuring similarity) with one image      
    (DupeSetMaster) considered the keeper image in the DupeSet.

** (DWTDTool)  **
Tool to facilitate allowing user to decide what to delete. User my “look” at candidates and make individual keep\toss decisions. 

** (DCDTool)  **
Create a tool that deletes chosen duplicates and performs final image library creation. (we need to decide what to do about files altered previously by other tools (PSP, and other editors).

** Additional Architecture Features **
1. All tools can be rerun multiple times and all operations can be “undone”.
2. Backs up images before doing deletions
3. Allows for restore of "all" deleted images or selected deleted images
4. Calculates "saved space" and other metrics  
5. Allows for user to delete the Deleted Items Repository
6. Allow for identification and “marking” of images that have detectable defects (blurry, too dark, or other bad image situations). 
7. Focus on use of Python for this process and leverage existing open source libraries for the bulk of this work.

** BIISTool Detailed Design **
1. provide for specification of a list of locations available to the local computer to search for duplicate files
2. Allow for recursive search of directories "starting" at each specified location
3. Capture EXIF and other data (a defined field list) about each image if the data exists in the image file or the directory location
  	A. This data will further aid in identifying the oldest of two images as well as get us closer to an exact "date taken" if possible (image dates can change when copied by different operating systems or can change when different tools "touch" them.  Not all images retain data - which must be accounted for.
4. Save all of the information into a data base for better query and join across the various fields
  A. Image physical path
  B. Image name
  C. Image based date, and camera (like manufacturer) Information
    1. EXIF or other "date taken"
  D. Other disk based information such as dates, file size, file extension
  E. result of initial image comparison "hash" for later image recognition
  F. retain image recognition calculated bitmap
5. Provide for display of "processing progress" information during scanning\processes allowing user to be aware of progress
