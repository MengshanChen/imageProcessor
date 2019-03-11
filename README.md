# Image Processor
**Class**: CPSC-5200-01 <br/>
**Author**: Mengshan Chen <br/>

## A high level overview of the full system
![architecture](https://user-images.githubusercontent.com/38142465/54092585-1676bf80-434b-11e9-93b5-a931cd17398d.png)

## Description of the architecture
Since transformation should run quickly and the users do not need to wait for a long time, the architecture will focus on using the server memory to run API service. Here is the flow: Clients will send requests with a image and a list of image transforms as part of the HTTP body through HTTPS to the server. The server receives the request and calls API to identify the operations in the request and execute the transforms in order on the in-memory image. At last, the server sends the image back through HTTPS so that the client can download it. 

## Communication protocols
This is a REST API which is based on the HTTP protocol. 

## API overview 
*	URL: /imageProcessor
* Method: POST
*	Parameters: <br />
  -name: "Transforms" <br />
   in: "formData" <br />
   description: "image operations" <br />
   required: true <br />
   type: "string" <br />
  -name: "file" <br />
   in: "formData" <br />
   description: "file to upload" <br />
   required: true <br />
   type: "file" <br />
*	Responses: <br />
	* 200 (Success) <br />
  	* 400 (Bad request)<br />

The file called "swagger.json" covers more details. 

## Sample Code 
(please view the following files)<br />
- Main files: “server.py” and “client.py” 
  * server.py: covers the API design
  * client.py describes the format of requests, and how to send requests to the server. 
- Demo files: 
  * demo.py: a web application to test the functionality 
  * index.html: UI
  
## Display
- After run main files: <br />
  ![Run](https://user-images.githubusercontent.com/38142465/54092613-6eadc180-434b-11e9-9297-b8f9b8123766.png)
  
- After run the demos: <br />
  ![demo](https://user-images.githubusercontent.com/38142465/54092641-c4826980-434b-11e9-9515-d191b03ded33.png)
  
## Specification
The API covers seven transforms. The client can combine operations into a string, and each one is separated by a comma without spaces and capitals. Also, each one should follow the following formats.  <br />
For example: flip_h,rotate_360,grayscale,w_400,h_500,rotate_left <br />

| Transfrom | Format |
| ------------- | ------------- |
| Flip Horizontal | flip_h |
| Flip Vertical | flip_v |
| Rotate Degree | rotate_angle |
| Rotate Left | rotate_left |
| Rotate Right | rotate_right |
| Convert to grayscale | grayscale |
| Resize width | w_length |
| Resize height | h_length |
| Thumbnail | thumbnail |


Use an image called "test.jpeg" in /static/images to show the transforms that serves as a proof of concept: <br />
* flip  <br />
![flip_v](https://user-images.githubusercontent.com/38142465/54093739-21841c80-4358-11e9-8392-cddc7de12bb2.jpeg)

* rotate angle <br />
![rotate_30](https://user-images.githubusercontent.com/38142465/54093747-3b256400-4358-11e9-8ebc-bb6a7f9eeca1.jpeg)

* rotate left <br />
![rotate_left](https://user-images.githubusercontent.com/38142465/54093764-5001f780-4358-11e9-8d9a-6bd160a73ff5.jpeg)

* rotate right <br />
![rotate_right](https://user-images.githubusercontent.com/38142465/54093767-5bedb980-4358-11e9-964f-7705c7663a6a.jpeg)

* grayscale <br />
![grayscale](https://user-images.githubusercontent.com/38142465/54093770-6314c780-4358-11e9-9c55-fa99216ff32b.jpeg)

* thumbnail <br />
![thumbnail](https://user-images.githubusercontent.com/38142465/54093773-6b6d0280-4358-11e9-886d-dcae28e44e95.jpeg)

* flip horizontal, grayscale  <br />
![flip_h,grayscale](https://user-images.githubusercontent.com/38142465/54093779-732ca700-4358-11e9-9cc1-424723f50c31.jpeg)


## Explanation of implementation
After receiving the requests, the API will check the format of the image. Legal file formats include “jpg”, “png” and “jpeg”. If the image meets the requirement, the API stores the original file in memory. If there already exists an image with the same file name, the file name of the coming image will be added the current datetime. <br />

Then, this API service will use the design pattern called Iterator Pattern. Because all transforms are combined in order into a string, the API will split the string into a list, iterate through the list, identify each transform and call the corresponding helper functions to modify the image. <br />

After transformation, a word “new” will be added to the file name so as to separate the original file and the modified image will be saved in memory. Then, the API sends this new image file to the user.  <br />

## Other consideration
Because this API specifies the format of transform, the API will return an error (400) if the client request does not meet this requirement. 
