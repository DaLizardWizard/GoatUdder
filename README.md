# GoatUdder
Pipeline that extracts stall number and goat udder sub-images from video frames taken in a milking parlour.  

Autodetecting stall number in goat images followed by udder extraction

Background
Two data streams were found that were critical to this preliminary study. 
The first an image folder containing 72 frames from a raw video of the goat milking parlour (an example frame is shown below in Figure 1) containing key information such as stall number and udder appearance.

![image](https://user-images.githubusercontent.com/75072026/136539551-74e4a401-36dc-450c-ab40-bb00843a0cb3.png)

Figure 1. Frame from goat milking video.

The second was a data table containing metadata about the goats such as udder score, birthdate and image ID, as per below.
Year     	Tag  		Stall    	Photo Old  	Photo New 	Score 
2014  	F05595    	 18  	SHF_0019315    1       		3
Method
Stall detection
A slice of the image spanning the width of the image was taken between pixel Y positions of 45:85 to extract the image area which contained the stall number information.(Below, Figure 2)

![image](https://user-images.githubusercontent.com/75072026/136539596-65f60cb9-5853-41fe-ac54-cd5bf7753527.png) 

Figure 2. Image slice containing key stall information.

From this image, only pixels after pixel X position 250 were used for further image enhancement and optical character recognition (OCR) as shown below (Left, Figure 3). This section was chosen as the stall numbers on the left side were at a distorted angle, attempts to correct this angle distortion were made such as a) rotation b) skewing and c) radon transforms but were all unsuccessful (data not shown). This image segment was then manipulated to enhance the blue area containing stall number by depleting channels containing red or green colours (Centre, Figure 3). Average pixel intensity was then determined across the horizontal image axis (Right, Figure 3). From this average pixel intensity plot, the leftmost position over a pixel intensity of 200 was determined to be the left-most bounds of the stall information panel and like-wise for the right-most bounds. From this, the rough centre of the stall information panel could be calculated.

![image](https://user-images.githubusercontent.com/75072026/136539615-9328a009-fc5c-4069-bb97-4b450e71fa10.png) 

Figure 3. A sub-slice of the image strip containing stall information was processed and enhanced to extract the left and right bounds of the stall information panel to use as coordinates for further text extraction. The leftmost pixel position above an average intensity of 200 was deemed to be the left bounds, likewise for the right bounds.

Stall number window extraction and processing
An image slice of a 40x40 pixel box centred between the left and right-most stall coordinates was taken from a greyscale version of the search strip (Left, Figure 4). This 40x40 box was then passed through a Yen threshold filter to produce a binary image required for OCR (Right, Figure 4).

![image](https://user-images.githubusercontent.com/75072026/136539646-c99c1495-adc5-4302-a264-5f07d7467462.png) 

Figure 4. Extraction of area containing the stall number (Left) and processing into a binary image (Right) required for OCR using a Yen threshold filter.

Automated stall number detection using OCR
The 40x40 binary image containing the stall number was then passed through Pytesseract, a wrapper for Google’s Tesseract-OCR Engine trained using a standard image similarity and neural-network character library. A page segmentation mode of 6 was used to treat the text as a single uniform block of vertically aligned text.
Automated udder image extraction
If the OCR could determine a character within the stall information window (either correct or incorrect), udders to both the left and the right of the stall were imaged using the centre of the stall information panel as a reference position (Below, Figure 5).

![image](https://user-images.githubusercontent.com/75072026/136539671-e3a6547c-a270-4ce0-99e6-81727348fece.png) 

Figure 5. Automated extraction of udders based upon stall number reference point.

Results
Successful stall processing examples

![image](https://user-images.githubusercontent.com/75072026/136539722-23bb8661-63f7-492c-9317-d33048d28176.png)

![image](https://user-images.githubusercontent.com/75072026/136539742-5834ef6d-b45f-4b49-a199-a63d9536c8d8.png)

![image](https://user-images.githubusercontent.com/75072026/136539750-a4867493-8344-4448-b455-804402f73d0e.png)

![image](https://user-images.githubusercontent.com/75072026/136539763-dfa7ab98-154d-46fb-88c8-a378d039c994.png)
  
   

Unsuccessful stall processing examples
![image](https://user-images.githubusercontent.com/75072026/136539799-8cbeb650-b80e-471f-b224-f40f067a9493.png)

![image](https://user-images.githubusercontent.com/75072026/136539809-6878ed0e-0522-4d62-a897-5f5b828e6318.png)

![image](https://user-images.githubusercontent.com/75072026/136539818-347309fb-85e5-43e5-924f-09ecad7884fa.png)

![image](https://user-images.githubusercontent.com/75072026/136539835-1bae4198-b92a-4213-b164-3e966dda7d9b.png)
   
   
Metadata matching
Unfortunately, there seemed to be no correlations between any information provided in or by the image with the metadata database provided. I could not find any alternative images or label lists within the various folders so at this point I cannot determine the absolute accuracy of the computer vision tool but rather demonstrate that it can perform the steps needed to reference a supplied database (Below, Figure 6).
 
Future improvements
Overall this stage of tool development shows a promising concept on a limited dataset which can perform accurately under certain conditions. To further improve the automated extraction of stall information and goat udder images the following actions are proposed:
1)	Increased resolution. Increasing the target resolution (within target area) of camera should aid in OCR. This can be achieved by either using a higher resolution camera of moving the camera closer so that it covers one or two stalls rather than three. 
2)	Increased framerate of video. Increasing the frame-rate of video will ensure that a “front on” number is read by the camera and will thus increase the chance of stall information being read correctly. Additionally, multiple images of each udder should allow a 3D model of the udder to be created using a process known as “Photogrammetry”. These 3D models can then be sued to aid udder scoring.
3)	Larger stall numbers. This should again make it easier for the OCR to read stall information correctly.
As all three of the main suggested improvements are relatively low tech and can be implemented swiftly, it is recommended that additional video data be collected to determine better accuracy of this video analysis pipeline.
