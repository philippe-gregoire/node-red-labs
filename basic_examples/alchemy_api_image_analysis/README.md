#  Node-RED Alchemy Vision (Image Analysis) Lab
## Overview
The Alchemy Vision service allows to analyse the contents of an image and extract features from it. The Face Detection service is able to identify multiple faces within the image, and determine their gender and age with a confidence score, and identify celebrities.

### Node-RED Alchemy Image Analysis (Vision) node
The Node-RED ![`Alchemy Image Analysis`](images/node_red_alchemy_image_analysis.png) node provides a very easy wrapper node that takes an image URL or binary stream as input, and produces a array of detected faces , age, bounding box, gender and name.

## Basic Alchemy Image Analysis Flow
### Flow overview
In this exercise, we will show how to simply generate the face recognition data from an image URL. The structure of the flow is very similar to the Watson Visual Recognition flow.  
The flow will present a simple Web page with a text field where to input the image's URL, then submit it to Alchemy Image Analysis, and output the faces that have been found on the reply Web page.  
![AlchVis-FaceDetectionFlow](images/alchvis_face_detection_flow.png)  

### Building the flow
The nodes required to build this flow are:  
  - A ![`HTTPInput`](/introduction_to_node_red/images/node_red_httpinput.png) node, configured with a `/alchvision` URL  
  - A ![`switch`](/introduction_to_node_red/images/node_red_switch.png) node which will test for the presence of the `imageurl` query parameter:  
   ![AlchVis-Lab-Switch-Node-Props](images/alchvis_switch_props.png)
  - A first ![template](/introduction_to_node_red/images/node_red_template.png) node, configured to output an HTML input field and suggest a few selected images taken from official sources:
```HTML
<h1>Welcome to the Alchemy Vision Face Detection Demo on Node-RED</h1>
<H2>Select an image</H2>
<form  action="{{req._parsedUrl.pathname}}">
    <img src="https://si.wsj.net/public/resources/images/MK-CK494_SELFIE_GR_20140303174816.jpg" height='100'/>
    <img src="https://upload.wikimedia.org/wikipedia/commons/f/f1/34th_G8_summit_member_20080707.jpg" height='100'/>
    <img src="http://demo1.alchemyapi.com/images/vision/politicians.jpg" height='100'/>
        <br/>Copy above image location URL or enter any image URL:<br/>
    Image URL: <input type="text" name="imageurl"/>
    <input type="submit" value="Analyze"/>
</form>
```
![AlchVis-Lab-TemplatePrompt-Node-Props](images/alchvis_template_prompt_props.png)

  - A ![change](/introduction_to_node_red/images/node_red_change.png) node to extract the `imageurl` query parameter from the web request and assign it to the payload to be provided as input to the Alchemy Image Analysis node:  
 ![AlchVis-Lab-Change_and_IA-Node-Props](images/alchvis_change_and_ia_props.png)

  - The ![`Alchemy Image Analysis`](images/node_red_alchemy_image_analysis.png) node. Make sure that you have installed and bound an instance of the `Alchemy API` ![AlchemyAPIService](images/alchemy_api_service.png) service to your Node-RED application in bluemix. Otherwise, you can edit the Alchemy Image Analysis node to provide an `apikey`.  
 
  - A ![`template`](/introduction_to_node_red/images/node_red_template.png) node with the following content, which will format the output returned from the Image Analysis node into an HTML table for easier reading:  
```HTML
    <h1>Alchemy Image Analysis</h1>
    <p>Analyzed image: {{payload}}<br/><img id="alchemy_image" src="{{payload}}" height="50"/></p>
    {{^result}}
        <P>No Face detected</P>
    {{/result}}
    <table border='1'>
        <thead><tr><th>Age Range</th><th>Score</th><th>Gender</th><th>Score</th><th>Name</th></tr></thead>
        {{#result}}<tr>
            <td><b>{{age.ageRange}}</b></td><td><i>{{age.score}}</i></td>
            <td>{{gender.gender}}</td><td>{{gender.score}}</td>
            {{#identity}}<td>{{identity.name}} ({{identity.score}})</td>{{/identity}}
        </tr>{{/result}}
    </table>
    <form  action="{{req._parsedUrl.pathname}}">
        <input type="submit" value="Try again"/>
    </form>
```
  - Finally, linked to the ![`HTTPResponse`](/introduction_to_node_red/images/node_red_httpresponse.png) output node. 
![AlchVis-Lab-TemplateReport-Node-Props](images/alchvis_template_report_props.png)

### Testing the flow
To run the web page, point your browser to  `/http://xxxx.mybluemix.net/alchvision` and enter the URL of some  image. The URL of the listed images can be copied to clipboard and pasted into the text field.  
The complete flow is available at [AlchVis-Lab-WebPage](alchvis_lab_webpage.json).

## [Alchemy Vision face identification lab with thumbnails](/advanced_examples/alchemy_image_analysis_thumbs/README.md)
