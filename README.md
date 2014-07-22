FORMAT: 1A
HOST: https://api.mywebsite.com

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

