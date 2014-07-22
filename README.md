FORMAT: 1A
HOST: https://api.mywebsite.com

# Knox Payments

##  Integration Instructions and APIs

No matter what you do with Knox, you'll need your API Key and API Password from Knox. If you're logged in, you'll see yours below. Otherwise, you'll see a sample:

```http
API_Key: TEST_ac3f10eaaef2830
API_Password: TEST_ce7d20740ed380b
```

# Group Basic HTML-CSS-JS Integration
Websites and web apps that don't need advanced API functionality. You can do almost everything you'll need to integrate by simple using our JS snippet, button, and callback API. 

## JS [/JS]
Place this Snippet after you load jQuery on the page. Customize the data-attributes to fit your needs - virtually every part of the payment process is customizable from this snippet. 

```js
<script src="https://knoxpayments.com/merchant/knox.js" id="knox_payments_script" button_text="PAY WITH YOUR BANK" api-key="7155_ac3f10eaaef2830" recurring="ot" user_request="show_all" invoice_detail="Describe what this payment is for" response_url=""></script>
```
+ Show Options
+ **recurring**: at what interval should this payment be made?
	+ **ot** = nonrecurring, single payment
	+ **weekly-day-number** = weekly recurring payment on this day of week for this number of payments. Day must equal monday, tuesday, wednesday, thursday, or friday. *(Example: weekly-monday-12)*
	+ **monthly-day-number** = monthly recurring payment on this day of the month for this number of payments. Day must be an integer 1 - 28. *(Example: monthly-28-10)*
	+ **yearly-month-day-number** = yearly recurring payment on this month on this day for this number of payments. Month (1-12) and day (1-28) must be integers. *(Example: yearly-7-4-3)* 	
+ **user_request**: after the payment is made, would you like any additional information from the user?
	+ show_all: get name, address, phone number, and email from the user. ***Recommended***	
+ **invoice_detail**: What do you want to show up on your customers' bank statements?
+ **response_url**: the URL Knox redirects to when a payment is done. This is where you get the response with the transaction id, as well as where you should show a "success" page or other page for your users when a payment is made. Leave blank to redirect onto the current page. 

## CSS [/CSS]
Place this CSS in the <head> of your document.

```js
<link rel="stylesheet" href="https://knoxpayments.com/merchant/knox.css">
```

## HTML [/HTML]
Place a div with the id of knox-payments-div where you want the Knox button to appear.

```html 
<div class="knox-payments-div" data-amount="10"></div>
```
Customize the data-amount to suit your needs. 

+ Show HTML Amount Options
+ If you want the user to set the amount by inputting the amount themselves, use:

	+ ```html 
	<div class="knox-payments-div" data-amount="custom"></div>
	```

+ Or if you want the button to represent a specific amount, such $35, use:
	+ ```html
	<div class="knox-payments-div" data-amount="35"></div>
	```

+ Or if you want to set the amount programmatically, override the id of "d_amout" for knox-payments-div. Here's a jQuery example of this where we're targeting a button that is inside of something with the class some_class:
	+ ```js
	//a variable representing an amount from, for example, a shopping cart
	var some_var = 40;
	//set the value of the button
	$(".some_class .knox-payments-div .d_amout").val(some_var);
	```
	
You can also easily add recurring payments using the data-recurring attribute 

+ Show HTML Recurring Options
+ If you want the user to set the interval themselves:

	+ ```html 
	<div class="knox-payments-div" data-recurring="custom"></div>
	```

+ Or if you want to set it to automatically be either weekly, monthly, or yearly - set the data-recurring to whichever you'd like it to be:
	+ ```html
	<div class="knox-payments-div" data-recurring="monthly"></div>
	```

+ Data-recurring and data-amount can be combined. For instance if you wanted recurring and amount to be dictated by the user:
	+ ```html
	<div class="knox-payments-div" data-recurring="custom" data-amount="custom"></div>
	```
+ Or for a button that makes monthly recurring payments of $20:
	+ ```html
	<div class="knox-payments-div" data-recurring="monthly" data-amount="20"></div>
	```

## Response [/response_url/Pst={Pst}&completed={completed}&pay_id{pay_id}]
The response will come to the response_url location you set in the script. It contains the follow scenarios: 

+ Parameters

	+ Pst (required, string, `68a5sdf67`) ... The note ID
    + completed (required, string, `68a5sdf67`) ... The note ID
    + pay_id (required, string, `68a5sdf67`) ... The note ID

+ Model

    + Body

            {
                "Pst": "Paid",
                "completed": "completed",
                "pay_id": "j8UiiPy"
            }


### Responses [POST]
The response will always return no matter what, but will return differently depending on whether a payment was finished, cancelled, or finished but the user declined to pass the extra information requested.

+ Response 200

    [Response][]

+ Response 200

    + Body

            {
                "Pst": "Paid",
                "completed": "declined_who",
                "pay_id": "j8UiiPy"
            }
    
+ Response 200

    + Body

            {
                "Pst": "Unpaid",
                "completed": "cancelled",
                "pay_id": "j8UiiPy"
            }
            
## Transaction_Details [/trans_details?trans_id={pay_id}]
The response will come to the response_url location you set in the script. It contains the follow scenarios: 

+ Parameters

    + pay_id (required, string, `68a5sdf67`) ... the pay_id of the payment you want details about (passed in the response)

+ Model

    + Body

            {
                "trans_details": {	
					"trans_id": "Y9rcdhr",
					"name": "Tommy Nicholas",
					"email": "tommy@knoxpayments.com",
					"address": "123 Fake St, Richmond, VA 23223",
					"phone": "8045551234"
                }
            }

### Get Transaction Details [GET]
The response will always return no matter what, but will return differently depending on whether a payment was finished, cancelled, or finished but the user declined to pass the extra information requested.

+ Response 200

    [Transaction_Details][]
    
# Group Token Payments

You may want to create tokens to allow you to make payments on behalf of a user programatically - similar to the way many Credit Card payment APIs like Stripe or Braintree function. This allows you to have the ability to make payments later for a customer, to send money from one user to another in a marketplace, create automatic payouts, etc. To make these payments, you need a special partner key from Knox. Contact tommy@knoxpayments.com to become a partner. 

## Create_Token [/create_token.php?PARTNER_KEY={PARTNER_KEY}&PARNTER_PASS={PARTNER_PASS}TRANS_ID={TRANS_ID}&LIMIT_REQ={LIMIT_REQ}]
To create a token, you must first have them make a payment for $0 using the Knox Payments button. Get the trans_id from the response using trans_details.php (as described in the HTML/CSS/JS section)

Call create_token.php with you partner key, api password, the trans_id, and a limit request (which is always 300 for now)
+ Parameters

    + PARTNER_KEY (required, string, `your_biz_name`)
    + PARTNER_PASS (required, string, `1asd_aao4uf4o`)
    + TRANS_ID (required, string, `1848ei5`)
    + LIMIT_REQ (required, number, `300`)
+ Model

    + Body

            {
                "JSonDataResult": {	
					trans_id: "Y9rcdhr",
					partner_key: "your_biz_name"
					req: "300"
					status_code: "inserted"
					user_key: "e467b9de5566ce36cfe2827505809f65",
					user_pass: "10d2472ca2cca265e8d0233a08ff8ff",
					error_code: null
                }
            }

### GET TOKEN [GET]

This call has four parameters, all of which are required. Remember: to make a pin, you first must have the person make a $0.00 payment and get the trans_id for that payment.

+ Show Parameters
+ PARTNER_KEY
	 + Partner API key (initiates PIN payment for this business - NOT your API Key)
	 + Example: knox
+ PARTNER_PASS
	 + Partner API Password
	 + Example: TEST_ce7d20740ed380b
+ trans_id
	 + The trans_id from the onboard payment retrieved in the onboard payment response. 
	 + Example: PID980018
+ LIMIT_REQ
	 + How much money per day you want to authorize this pin to be able to make (max 300 currently)
	 + Example: 300 (always 300 for now)

+ Response 200
	
	[Create_Token][]

## Token to Business Payments [/pinpayment.php?payee_key={payee_key}&payee_pass={payee_pass}PARTNER_KEY={PARTNER_KEY}&trans_id={trans_id}&payor_key={payor_key}&payor_pass={payor_pass}&amount={amount}&recur_status={recur_status}]

You can use a token to make a payment to your business using your API Key and your API Password as the payee_key and payee_pass parameters. You can also do the reverse and pay out to a token using your API Key and API Password as the payor_key and payor_pass parameters. 


+ Parameters

	+ payee_key (required, string, `TEST_ac3f10eaaef2830`)
	+ payee_pass (required, string, `TEST_ce7d20740ed380b`)
	+ PARTNER_KEY (required, string, `your_biz_name`)
	+ trans_id (required, string, `Y9rcdhr`)
	+ payor_key (required, string, `e467b9de5566ce36cfe2827505809f65`)
	+ payor_pass (required, string, `10d2472ca2cca265e8d0233a08ff8ff`)
	+ amount (required, string, `10`)
	+ recur_status (required, string, `ot`)

+ Model

    + Body
    
            {
                "JSonDataResult": {	
					error_code: null,
					status_code: "PAID",
					trans_id: "Y9rcdhr"
                }
            }
            
### Make Token to Business Payment [GET]

The call takes several parameters, all of which are required

+ Show Parameters
+ payee_key
	 + Business API Key for party receiving funds (payee - in this case your business)
	 + Example: TEST_ac3f10eaaef2830
+ payee_pass
	 + Business API Password for party receiving funds (payee - in this case your business)
	 + Example: TEST_ce7d20740ed380b
+ trans_id
	 + Payment ID (must be unique)
	 + Exmaple: PID980018
+ PARTNER_KEY
	 + Partner key (initiates PIN payment for this business - NOT your API Key)
	 + Example: knox
+ payor_key
	 + User ID making the payment (payor)
	 + Example: 46c1b7b3c98a925e1d3a3227795f3b03
+ payor_pass
	 + User PIN making the payment (payor)
	 + Example: 46c1b7b3c98a925e1d3a3227795f3b04
+ amount
	 + The amount of the payment (must be greater than 0)
	 + 1.01
+ recur_status
	 + Recurring status for payment
	 + **ot** = nonrecurring, single payment
	 + **weekly-day-number** = weekly recurring payment on this day of week for this number of payments. Day must equal monday, tuesday, wednesday, thursday, or friday. *(Example: weekly-monday-12)*
	 + **monthly-day-number** = monthly recurring payment on this day of the month for this number of payments. Day must be an integer 1 - 28. *(Example: monthly-28-10)*
	 + **yearly-month-day-number** = yearly recurring payment on this month on this day for this number of payments. Month (1-12) and day (1-28) must be integers. *(Example: yearly-7-4-3)*

	 
If the response error code is null, everything went well. Otherwise, the error code text will tell you what went wrong. 

+ Show Error Codes
+ Invalid Trans__ID(DUP)
	 + Duplicate trans id. (please ensure this never happens) 	
+ Trans_ID fail
	 + Invalid trans id. (please ensure this never happens)
+ Invalid Trans__ID(VAR)
     + Trans_ID contains invalid number of characters. (please ensure this never happens)
+ Invalid trans amount
     + Trans_amount should be  > 0
+ Invalid PIN
     + Invalid userid or password in pin based transaction
+ Invalid api key
     + Partner key or merchant API key does not contain permission to make PIN-based payment.
+ Invalid UserName or Password
     + Invalid USER_PIN
+ Invalid UserName or Password
     + Invalid USER_PASS
+ Authorization Invalid
     + Invalid Partner_key or password / Invalid user id or user pin


+ Response 200
	
	[Token to Business Payments][]

## Token to Token Payments [/pinpayment.php?payee_key={payee_key}&payee_pass={payee_pass}PARTNER_KEY={PARTNER_KEY}&trans_id={trans_id}&payor_key={payor_key}&payor_pass={payor_pass}&amount={amount}&recur_status={recur_status}]

+ Parameters

	+ payee_key (required, string, `TEST_ac3f10eaaef2830`)
	+ payee_pass (required, string, `TEST_ce7d20740ed380b`)
	+ PARTNER_KEY (required, string, `your_biz_name`)
	+ trans_id (required, string, `Y9rcdhr`)
	+ payor_key (required, string, `e467b9de5566ce36cfe2827505809f65`)
	+ payor_pass (required, string, `10d2472ca2cca265e8d0233a08ff8ff`)
	+ amount (required, string, `10`)
	+ recur_status (required, string, `ot`)

+ Model

    + Body
    
            {
                "JSonDataResult": {	
					error_code: null,
					status_code: "PAID",
					trans_id: "Y9rcdhr"
                }
            }
            
### Make Token to Token Payment [GET]

The call takes several parameters, all of which are required

+ Show Parameters
+ payee_key
	 + User ID of party receiving funds (payee)	
	 + Example: 46c1b7b3c98a925e1d3a3227795f3b05
+ payee_pass
	 + User PIN of party receiving funds (payee)
	 + Example: 46c1b7b3c98a925e1d3a3227795f3b0q
+ trans_id
	 + Payment ID (must be unique)
	 + Exmaple: PID980018
+ PARTNER_KEY
	 + Partner API key (initiates PIN payment for this business - NOT your API Key)
	 + Example: knox
+ payor_key
	 + User ID making the payment (payor)
	 + Example: 46c1b7b3c98a925e1d3a3227795f3b03
+ payor_pass
	 + User PIN making the payment (payor)
	 + Example: 46c1b7b3c98a925e1d3a3227795f3b04
+ amount
	 + The amount of the payment (must be greater than 0)
	 + 1.01
+ recur_status
	 + Recurring status for payment
	 + **ot** = nonrecurring, single payment
	 + **weekly-day-number** = weekly recurring payment on this day of week for this number of payments. Day must equal monday, tuesday, wednesday, thursday, or friday. *(Example: weekly-monday-12)*
	 + **monthly-day-number** = monthly recurring payment on this day of the month for this number of payments. Day must be an integer 1 - 28. *(Example: monthly-28-10)*
	 + **yearly-month-day-number** = yearly recurring payment on this month on this day for this number of payments. Month (1-12) and day (1-28) must be integers. *(Example: yearly-7-4-3)*

	 
If the response error code is null, everything went well. Otherwise, the error code text will tell you what went wrong. 

+ Show Error Codes
+ Invalid Trans__ID(DUP)
	 + Duplicate trans id. (please ensure this never happens) 	
+ Trans_ID fail
	 + Invalid trans id. (please ensure this never happens)
+ Invalid Trans__ID(VAR)
     + Trans_ID contains invalid number of characters. (please ensure this never happens)
+ Invalid trans amount
     + Trans_amount should be  > 0
+ Invalid PIN
     + Invalid userid or password in pin based transaction
+ Invalid api key
     + Partner API key or merchant API key does not contain permission to make PIN-based payment.
+ Invalid UserName or Password
     + Invalid USER_PIN
+ Invalid UserName or Password
     + Invalid USER_PASS
+ Authorization Invalid
     + Invalid Partner_key or password / Invalid user id or user pin


+ Response 200
	
	[Token to Token Payments][]
	
	
	
	
# Group Android

So you're trying to use Knox as a payment method in your Android app? We've built a sample application for you to look at and help guide you in your Android integration.

[Download the Android Example!](http://localhost:8888/admin/docs/bin/KnoxAndroidExample.zip)

## Your app!

Your native app will include a Knox Payments button that links to our online payment flow.


Here's an example of what to put in the activity where you're accepting payments. Check it out and read those comments!
```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.fragment_main);
        
        // This gets extra info from the Knox webview after a payment is complete
        Intent intent = this.getIntent();
        Bundle extras = intent.getExtras();
        
        // If this is true, the payment has already run and come to some end state
        if (extras!=null) {
        	// Getting the return URL
        	String uri = extras.get("url").toString();
        	
        	TextView confText = (TextView) findViewById(R.id.confirmation);
        	// If the transaction has been canceled by the user
        	if (uri.contains("transactionCanceled")) {
        		confText.setText("This transaction was canceled by the user.");
        	} else {
        		/* 
        		 * If the transaction was successful, let's return the transaction ID to the native app. 
        		 * This isn't necessary, but it could be good to show confirmation in the app.
        		 */
        		String[] split = uri.split("&");
                String last = split[split.length-1];
                
                // Sets the text in the confirmation box to payid=<something> 
                confText.setText(last);
        	}
        };
    }
```

Function for the Knox button to launch a new payment. This switches to the webViewActivity class and starts the payment with Knox.
```
    public void launchPayment(View view) {
    	Intent myIntent = new Intent(MainActivity.this, WebViewActivity.class);
    	MainActivity.this.startActivity(myIntent);
    }
```

## Knox WebView

This is where the magic happens.

This class deals with the Knox Payment handoff. When you click "Pay with Knox" in the native app, this WebView opens to Knox, deals with the payment, and then hands the details of the transaction back to the native application.

```
public class WebViewActivity extends Activity {
    /** Called when the activity is first created. */

    WebView web;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.fragment_main);
```

In this section, add all of the parameters for your payment

```
        HashMap<String, String> map = new HashMap<String, String>();
        /*
         * In this section, add all of the parameters for your payment
         */
        // The payment amount
        map.put("amount", "0.01");
        
        // Your API key
        map.put("api_key", "8218_89068bb54040fb9");
        
        // Payment description for your records
        map.put("invoice_detail", "Describe this payment no spaces");
        
        // Would you like to set up a recurring payment? If not, this should be set to "ot". Check out the docs for more info
        map.put("recurring", "ot");
        
        // Information to get from the user after the payment (do you want to request their address and personal info?)
        map.put("information_request", "show_all");
        
        /*
         * The URI to return to after the payment - in this example, the return after a successful payment looks like this:
         * knox://knoxpayments.com/?pst=Paid&completed=completed&pay_id=1cjg9Ps
         * 
         * Use a URI starting with "knox://" so the native app can pick up the return call. You can customize this
         * callback in the code below if you want.
         */
        map.put("redirect_url", "knox://knoxpayments.com");
        
        /*
         * This is the URI to return to if the buyer cancels the transaction before it completes.
         * In this example, the return would look like this after a cancellation:
         * knox://knoxpayments.com/?pst=Unpaid&completed=canceled&pay_id=undefined
         * 
         * The listener for this URI can also be customized in the code below
         */
        map.put("cancel_url", "knox://knoxpayments.com/transactionCanceled");
        
        /*
         * This specifies text to display to the buyer about why the merchant is requesting their information after the payment.
         * Leave as "default", since this is an alpha feature that is not fully documented at this time
         */
        map.put("why_who", "default");
        
        // This stringifies the map parameters into a GET request and appends it to the URL of our server 
        String strMap = map.toString();
    	strMap = strMap.replace(", ", "&");
    	strMap = strMap.replaceAll("[{|}| ]","");
    	String url = "https://knoxpayments.com/newflow/index.php?" + strMap;
```

Load the URL for payment processing. Javascript must be turned on within the webview or the page will not load completely

```
    	// Load the URL for payment processing
    	//Javascript must be turned on within the webview or the page will not load completely
        web = new WebView(this);
    	web.getSettings().setJavaScriptEnabled(true);
        web.setWebViewClient(new myWebClient());
        web.loadUrl(url);
        setContentView(web);
    }
```

This part listens for URIs that start with "knox://", so the native app can detect that the transaction is finished and return to control.

```
    public class myWebClient extends WebViewClient
    {
        // Listens for URIs that start with "knox://", so the native app can detect that the transaction 
    	// is finished and return control
        @Override
        public void onLoadResource(WebView view, String url) {
            if (url.startsWith("knox://")) {
            	/*
            	 * If the URI starts with what we're listening for, transfer control back to the native app's
            	 * intent. The URI is also transfered over to that intent, so you can get things like the payid
            	 * from the transaction
            	 */
            	Intent myIntent = new Intent(WebViewActivity.this, MainActivity.class);
                myIntent.putExtra("url", url);
                WebViewActivity.this.startActivity(myIntent);
            }
        }
    }
```

## Pretty buttons!

We translated the classic look and feel of our design team's crown jewel, the "Pay with Knox" button into Android! If you check out the layout files included, you can use our styling. If you think it looks ugly, you can use your own! (Just don't tell Taylor)

![KnoxBtn.png](http://localhost:8888/admin/docs/bin/KnoxBtn.png)

Check out that sexy gradient.

[Just give me the styles! (Download Android layouts and shapes and such)](http://localhost:8888/admin/docs/bin/KnoxStyles.zip)

## Denouement

Hope this helps! If you have questions, feel free to email charles@knoxpayments.com and tell him that you'll buy him a root beer if he helps you with your app!

