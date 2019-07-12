Project name and description
This SDK allows you to quickly integrate Xsolla Login Widget with your website.
Currently SDK supports following types of authorization:
●	via login/password
●	via social networks
More methods on its way.
The library is used on Windows 7, 8 and macOS.
Getting started
Connect Xsolla Login Javascript SDK:
●	If your project uses Bower, launch the console and run the following command:
bower install xsolla-login-js-sdk

●	If you don’t have the package installed, add the following code to the <head> tag of the web page where you want to place the widget:
<script src="https://cdn.xsolla.net/xsolla-login-widget/sdk/2.1.1/xl.min.js"></script>

Step 2
Add the widget initialization code to the <body> tag.
<script type="text/javascript">
XL.init({
  projectId: '{Login ID}',
  callbackUrl: '{callbackUrl}'
});
</script>
Parameter	Description
projectId	Login ID from Publisher Account. Required.
callbackUrl	URL to redirect the user to after authentication. Must be identical to Callback URLspecified in Publisher Account in Login settings. Required if there are several Callback URLs.
Step 3
Select the way of placing the widget on the website:
●	fullscreen mode
●	particular block of the page.
FULLSCREEN MODE
Add the button with an on-click event to your website and call the XL.show() function.
<button onclick="XL.show()">Fullscreen widget</button>
The fullscreen mode will be closed upon clicking outside the widget.
BLOCK OF THE PAGE
Add the block, in which the widget will be placed, to the <body> tag of this page and specify the block ID.
<div id="xl_auth"></div>
Add the following script and specify the parameters as described below.
<script type="text/javascript">
var options = {
  width: 400,
  height: 550
};
XL.AuthWidget(element_id, options);
</script>
Parameter	Description
options	Login Widget block settings. The object consists of the parameters listed below.
width	Block width in pixels. Default is 400.
height	Block height in pixels. Default is 550.

________________________________________

Problems:
1.	I can’t find Login ID. Where is it?
Answer: Please go to your Publisher Account > Login settings > General settings > Login ID.
2.	Where can I find the guide described full integration of Xsolla Login?
Answer: Please follow the link.
