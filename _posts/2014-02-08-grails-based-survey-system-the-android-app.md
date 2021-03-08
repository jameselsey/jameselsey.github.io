---
title: 'Grails based survey system, the android app'
date: '2014-02-08T10:56:39+10:00'
permalink: /grails-based-survey-system-the-android-app
author: 'James Elsey'
category:
    - Android
    - Grails
    - Java
tag:
    - android
    - google
    - grails
    - maven
    - mobile
    - qr
    - qrcodes
---
Some time back I wrote an [article](/getting-to-grips-with-grails-building-a-survey-management-system) describing the roosearch system I developed using grails. This is the second part, the android client, please checkout the previous article otherwise this might not make much sense!

After completing the [grails component](https://github.com/jameselsey/roosearch-web), I had a RESTful API available to me, and I just needed to build an app that could consume those services.

#### Customer lookup and QR codes

The app needs to be simple and quick to use, one of the things I remember from a UX discussion at DroidCon UK is “Don’t annoy your users, they control your app ratings and your income!”. In order to lookup the surveys quickly, I’ve added the ability to scan QR codes. Actually I didn’t have to do a great deal as there is already an app called [ZXing](https://play.google.com/store/apps/details?id=com.google.zxing.client.android&hl=en_GB) by Google that scans QR codes, so I just needed to make Roosearch delegate to ZXing and handle the result.

Of course, we don’t want to exclude users that don’t have ZXing, or even a camera on their device, so I’ve also provided a text field where they can enter the customer Id manually if required.

When the user clicks on the “scan barcode” button, I first check if ZXing is installed using the following

```java
    public void scanBarCode(View v) {
        final boolean scanAvailable = isIntentAvailable(this,
                "com.google.zxing.client.android.SCAN");
        if (!scanAvailable){
            Toast.makeText(this, "You need to install the ZXing barcode app to use this feature", Toast.LENGTH_SHORT).show();
            return;
        }

        Intent intent = new Intent("com.google.zxing.client.android.SCAN");
        intent.putExtra("SCAN_MODE", "QR_CODE_MODE");
        startActivityForResult(intent, 0);
    }
```

If the user does have ZXing installed on their device, and choose to use it, we can get the result back from the bar code scan using:

```java
public void onActivityResult(int requestCode, int resultCode, Intent intent) {
        if (requestCode == 0) {
            if (resultCode == RESULT_OK) {
                String contents = intent.getStringExtra("SCAN_RESULT");
                performRooLookup(contents);
            } else if (resultCode == RESULT_CANCELED) {
                // Handle cancel
            }
        }
    }

    private void performRooLookup(String rooId) {
        if (StringUtils.isBlank(rooId)) {
            Toast.makeText(this, "Please enter a valid customer id", Toast.LENGTH_SHORT).show();
            return;
        }

        Integer customerId;
        try {
            customerId = Integer.parseInt(rooId);
        } catch (NumberFormatException e) {
            Toast.makeText(this, "Customer id needs to be numeric", Toast.LENGTH_SHORT).show();
            return;
        }
        new FindRooTask(this, new FindRooTaskCompleteListener()).execute(customerId);
    }
```

I then have the following buried in a service call, invoked by an AsyncTask, which handles finding Customer details:

```java
    public Customer getCustomerDetails(int customerId) {

        try {
            final String url = "http://roosearchdev.jameselsey.cloudbees.net/api/customer/{query}";

            HttpHeaders requestHeaders = new HttpHeaders();

            // Create a new RestTemplate instance
            RestTemplate restTemplate = new RestTemplate();
            restTemplate.getMessageConverters().add(new MappingJacksonHttpMessageConverter());

            // Perform the HTTP GET request
            ResponseEntity<Customer> response = restTemplate.exchange(url, HttpMethod.GET,
                    new HttpEntity<Object>(requestHeaders), Customer.class, customerId);

            return response.getBody();
        } catch (Exception e) {
            System.out.println("Oops, got an error retrieving from server.. + e");
        }
         return null;
    }
```

A Customer looks like this:

```java
public class Customer implements Parcelable {

    @JsonProperty("company_name")
    private String companyName;
    private String twitter;
    private String facebook;
    private List<SurveySummary> surveys = new ArrayList<SurveySummary>();
    //Accessors omitted
}
```

The SurveySummary just has a title and Id. The reason for just returning summaries is because a customer may have many surveys, and there is no need to obtain them all, we just obtain the title to display to the user, if selected, we’ll retrieve the survey by its id.

To recap, here are 2 screenshots that show the above; the landing screen, and then the customer display screen

![Landing screen for Roosearch, where the user can enter a customer Id or scan a QR code](/assets/post_images/2013/Screen-Shot-2013-12-23-at-15.51.01.png)

![Customer screen, display social media links, name, photo, and list of surveys that the customer has](/assets/post_images/2013/Screen-Shot-2013-12-23-at-15.56.16.png)

#### The survey engine

This is where the magic happens. I have a single activity and single view that handles presenting the survey to the user. As the surveys can change number of questions, and number of responses, I needed a way of dynamically traversing the survey object and allowing user to move between the questions whilst retaining state of what they have selected so far.

I’ve created the following method that will redraw the layout for a given question id:

```java
    public void drawQuestionOnScreen(int id) {
        TextView question = (TextView) findViewById(R.id.question);
        question.setText(s.getQuestion(id - 1).getText());   // subtract 1 as lists are indexed from 0

        LinearLayout linLay = (LinearLayout) findViewById(R.id.answers);
        linLay.removeAllViews();
        RadioGroup rg = new RadioGroup(this);
        rg.setId(1);
        for (int aIndex = 0; aIndex < s.getQuestion(id - 1).getResponses().size(); aIndex++) {
            Answer a = s.getQuestion(id - 1).getAvailableOption(aIndex);
            RadioButton button = new RadioButton(this);
            button.setText(a.getText());
            button.setTextColor(R.color.dark_text_color);
            button.setId(aIndex);
            rg.addView(button);
        }
        linLay.addView(rg);

        TextView status = (TextView) findViewById(R.id.status);
        status.setText(format("%d of %d", id, s.getQuestionCount()));
    }
```

As you can see, it will retrieve the question by Id, then iterate over the responses and generate RadioButtons. Moving to the next question is reasonably easy, firstly I work out if an option has been selected, and prevent moving on if not. After that, I mark the selected response in the survey object, and then work out if there is another question in the sequence to display, if not we can progress to the finish.

![One of the questions in the given survey](/assets/post_images/2013/Screen-Shot-2013-12-23-at-15.56.35.png)

```java
    public void next(View v) {
        RadioGroup rg = (RadioGroup) findViewById(1);

        int selectedRadioId = rg.getCheckedRadioButtonId();
        if(selectedRadioId == -1){
            Toast.makeText(this, "Please select a response", Toast.LENGTH_SHORT).show();
            return;
        }

        s.getQuestion(questionIndex - 1).getResponses().get(selectedRadioId).setSelected(true);
        // work out if there is another question, then move to it
        if (s.getQuestionCount() > 1 && questionIndex < s.getQuestionCount()) {
            questionIndex++;
            drawQuestionOnScreen(questionIndex);
        } else {
            // if there are no other questions, show dialog saying submit or not
            Toast.makeText(this, "Reached the end of the survey", Toast.LENGTH_SHORT).show();
            // HERE we should process the entire survey, crunch data and post off (maybe async)

            Intent i = new Intent(this, SurveyComplete.class);
            i.putExtra("com.roosearch.domain.Survey", s);
            startActivity(i);
        }
    }
```

A similar approach is needed for moving back to previous questions, determine if there is a previous question to move to then redraw the screen, like so:

```java
    public void previous(View v) {
        // work out if there is a previous question, and if so move to it
        if (s.getQuestionCount() > 1 && questionIndex > 1) {
            questionIndex--;
            drawQuestionOnScreen(questionIndex);
        } else {
            //if there are no other questions, move back to home screen, finish() this and scrap any progress
            finish();
        }
    }
```

Once the user completes all questions, the SurveyComplete activity is invoked.

#### Completing a survey

When the user has completed all questions, the survey object is passed into the SurveyComplete activity, which handles sending the responses back to the grails web application.

```java
@Override
    protected void onResume()
    {
        super.onResume();
        TextView tv = (TextView) findViewById(R.id.completeMessage);
        tv.setText("Thank you for taking the time to complete the survey");
        tv.setTextColor(R.color.dark_text_color);

        Survey s = getIntent().getExtras().getParcelable("com.roosearch.domain.Survey");

        if (s != null)
        {
            StringBuffer sb = new StringBuffer();
            sb.append("\n" + s.getTitle() + "\n");
            for (Question q : s.getQuestions())
            {
                sb.append("\nQ: " + q.getText());
                sb.append("\nA: " + q.getSelectedAnswer() + "\n");
            }
            tv.append("\n\n" + sb.toString());
        }

        new SurveyUploadTask(this, new SurveyUploadTaskCompleteListener()).execute(s);
    }

    public class SurveyUploadTaskCompleteListener implements AsyncTaskCompleteListener<Void> {
        @Override
        public void onTaskComplete(Void voidz) {
            Toast.makeText(SurveyComplete.this, "Survey uploaded", Toast.LENGTH_SHORT).show();
        }
    }
```

The activity uses an AsyncTask to post the data back to the grails API controller, and displays a toast when successful.

![Survey completed, results uploaded, and summary presented to user](/assets/post_images/2013/Screen-Shot-2013-12-23-at-15.57.08.png)

#### Wrapping it up

Overall quite a simple app, I spent probably around 2 or 3 weekends putting together, most of that time was spent getting to grips with some automated testing for android. The code is admittedly a little rough around the edges, but I was aiming for an MVP (most viable product) to get working, feel free to contribute or suggest improvements!

I chose to use maven, but would use gradle if I were to pick this up again. Be sure to check out the code on github and try running it against Roosearch web, it does work!

#### [Click here for the source code on Github](https://github.com/jameselsey/roosearch-mobile)