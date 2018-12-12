---
layout: post
title:  "An Exploration of the Android Text To Speech API"
categories: Android
tags: [android]
---
A practical attempt to understand the powerful Text To Speech of Android.

## Introduction
Right now all the android phones have the ability to speak out the texts of a screen via a generated voice. How does Android convert the texts into the speech then? Android Text To Speech is the answer. It is one of the powerful Android features that can allow users to convert plain text into speech that is synthesized by the Android TTS engine. It has a variety of usages, and its main purpose is to give users the ability to hear the texts spoken out loud in their android phones without the need to look at them. It enables users to do other things while listening to the contents of an app. It also helps illiterate users or users with disability to understand the texts in an app without difficulty. TTS was initially introduced in Android 1.6 API 4 in 2009, and it gradually becomes mature as the Android programmers invest their times in improving the feature. Right now, all Android platforms that has an API level bigger than API 4 can support the TTS feature. In order to really understand the Android Text To Speech feature, we need to first explore a few aspects of Android TTS engine that is important to the TTS-enabled application, and then understand how to incorporate the TTS feature into our application and configure the TTS engine correctly to make our application speak by showing a TTS-enabled example app.

A workflow diagram like below is also helpful for understanding how to incorporate the TTS feature into an app: 
<p><img src="/assets/images/text-to-speech-workflow.jpeg" alt="s" width="400" height="930" /> </p>
I will explain all parts of the TTS workflow diagram section by section and also implement the example app according to this workflow diagram.

## TTS Engine
The TTS engine is an android framework that provides the android app the ability to perform speech synthesis that converts the texts inside the app into a spoken voice output. It is included in the Android platform, and it supports a large number of languages such as Chinese, English, French, Italian, Japanese, Russian, and Spanish, etc.. It also supports a variety of dialects from the same language (e.g. British English, American English, Canadian French, etc.) because the same language can be spoken differently in different regions of the world. In order to make the TTS engine work correctly in an app, we have to first initialize a TextToSpeech instance in that app's activity via its constructor that takes the application's context and TextToSpeech.OnInitListener. The TextToSpeech.OnInitListener must be configured because we need to define the behaviors of the TextToSpeech engine so that it can correctly speak the texts according to our needs. Here is an example for what an initialization of TextToSpeech instance in a MainActivity looks like:

```java
public class MainActivity extends AppCompatActivity {
   TextToSpeech mTextToSpeech;

   @Override
   protected void onCreate(Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);
       setContentView(R.layout.activity_main);
       // initialization of TextToSpeech Instance
       mTextToSpeech = new TextToSpeech(getApplicationContext(), new TextToSpeech.OnInitListener() {
           @Override
           public void onInit(int status) {
               // if initialization is successful, define the behaviors of the TextToSpeech engine
               if (status == TextToSpeech.SUCCESS) {
                   // set the language to be American English
                   Locale language = Locale.US;
                   int isSupported = mTextToSpeech.setLanguage(language);
                   if (isSupported != TextToSpeech.LANG_AVAILABLE && isSupported != TextToSpeech.LANG_COUNTRY_AVAILABLE) {
                       Toast.makeText(getApplicationContext(),"The current language " + language.toString()+ " is not available.",
                               Toast.LENGTH_SHORT).show();
                   } else {
                       // it is supported
                       int statusForPitch = mTextToSpeech.setPitch(2); // set the pitch to be normal
                       int statusForRate = mTextToSpeech.setSpeechRate(2); // set the speech rate to 2
                       if (statusForPitch != TextToSpeech.SUCCESS) {
                           Toast.makeText(getApplicationContext(), "Fail to set pitch", Toast.LENGTH_SHORT).show();
                       }
                       if (statusForRate != TextToSpeech.SUCCESS) {
                           Toast.makeText(getApplicationContext(), "Fail to set the Speech rate", Toast.LENGTH_SHORT).show();
                       }
                   }
               }
           }
       });
   }
}
```

In the above example, we first check the status of the initialization of the TextToSpeech instance. If it is successful, we move on to define the behaviors of this instance. We first set the language via `mTextToSpeech.setLanguage(language)` to load and set language, in the country `US`, to our TextToSpeech engine. That method takes Locale object (i.e. `Locale.US`) as a parameter and return an integer (e.g. `TextToSpeech.LANG_AVAILABLE`, `LANG_COUNTRY_AVAILABLE`, and `LANG_NOT_SUPPORTED`, etc.) to indicate the status of the chosen Locale. We can also set the pitch of the speech for the engine via `mTextToSpeech.setPitch(2)` and its speech rate via `mTextToSpeech.setSpeechRate(2)`, which both take a float as input and return either `TextToSpeech.SUCCESS` or `ERROR` for status checking. The setPitch method's default pitch value is 1, which is normal pitch. The higher value of the input increases the pitch, whereas the low value lowers it. The setSpeechRate method works like the setPitch method except that it sets the speech rate. These two methods essentially allow us to determine the synthesized speech's tone and speed.

## Language and Locale
Note that in the above example code for MainActivity class, we mention about the Locale object that is the input parameter of the TextToSpeech.setLanguage() method. This Locale object is provided by android operating system, and it essentially tells the TextToSpeech engine which language to load. If we look into the Locale object, we can see that it supports various accents of the same languages (e.g. Canada French, France French, etc.). We can use Locale.getAvailableLocales() to get the variety of the same language in different regions of the world (e.g. new Locale("en").getAvailableLocales()).  We can also check if a particular language is supported by an android device through TextToSpeech.isLanguageAvailable(Locale), and that method will return back a support status code for the given Locale to notify us about its availability. For example, the calls:

```java
mTextToSpeech.isLanguageAvailable(Locale.US);
mTextToSpeech.isLanguageAvailable(Locale.UK);
```

will return TextToSpeech.LANG_COUNTRY_AVAILABLE to indicate that both the language and the country are available.

While setting up the language for the engine, we have to make sure that the text's language is a match for the engine so that we don't make funny speech that is hard to understand.

## Speak out loud!
Once we successfully initialize the TextToSpeech instance, configure the engine correctly, and define its behaviors we can move on to actually make the app take in a string input to synthesize the speech and speak it out loud. To do all these, we will use the `TextToSpeech.speak(CharSequence text, int queueMode, HashMap<String, String> params)` to speak the text we want. Let's look at the following codes:

```java
mTextToSpeech.speak("Text To Speech is cool!", TextToSpeech.QUEUE_FLUSH, null)
mTextToSpeech.speak("Text To Speech is not hard to understand.", TextToSpeech.QUEUE_ADD, null)
mTextToSpeech.speak("Text To Speech is easy.", TextToSpeech.QUEUE_ADD, null)
```

We see that some constants about Queue is mentioned in the code. It is because the TTS engine maintains a global queue for all the text entries to synthesize, which are also called "utterances". Since each instance of TextToSpeech has its own queue, we need to flush the queue so that it can control which text entry can interrupt the current one and which one is queued. Hence, the first `speak()` flushes the queue and speaks its text entry, and the second and third `speak()` calls add their text entries into the queue in a first-in-first-out manner and speak them in order defined by the queue after the first `speak()` call is finished. Note that the `speak()` is asynchronous, which means all the `speak()` calls are already finished before all the speeches from the queue have been synthesized and spoken out loud, regardless of the use of QUEUE_ADD or QUEUE_FLUSH. The optional params in the `speak()` method provides a way to perform other tasks after a `speak()` call is finished, and it can be set to null if we just don't want to do anything after the `speak()` call is finished. We can also implement UtteranceProgressListener to define what to do before starting (i.e. `OnStart()`), what to do after finishing speaking (i.e. `OnDone()`), and what to do when there is an error (i.e. `OnError()`). The implementation of UtteranceProgressListener is not necessary for the `speak()` to work properly. 

## Audio file and playback
While the `speak()` method of TextToSpeech synthesizes and speaks the input text right away, we may want to store the synthesized speech as an audio files because we may choose to speak the same text repeatedly in our app. If we choose to use `speak()` to speak the same text all the time, the overhead burden for CPU to process the speech synthesis will increase unnecessarily and a lot of resources (e.g. memory, computation time, etc.) are wasted in the repeated `speak()` calls for the same text. To avoid this issue, we can use the `TextToSpeech.synthesizeToFile(String text, HashMap<String, String> params, String filename)` to synthesize the speech for the given text and store it into an .wav audio file. The example codes are the following:

```java
String speechText = "Text To Speech synthesize to file."
String audioFilePathName = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_DOCUMENTS).getPath() + "audio.wav"
mTextToSpeech.synthesizeToFile(speechText, null, audioFilePathName)
```

As we can see in the above codes, we use `synthesizeToFile()` to synthesize the speechText and store it as audio.wav file under the directory of Documents. Note that we have to edit the AndroidManifest.xml to give the app permissions for writing the audio file into the file storage of the android phone. We just need to add the following into the AndroidManifest.xml:

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

Alternatively, we can use `synthesizeToFile(CharSequence text, Bundle params, File file, String utteranceId)` which does the same job as the previous method. The only difference is the parameters of these two methods.

If we know there is an existed audio file that corresponds to a given text in the device, we can use `TextToSpeech.addSpeech(CharSequence text, File file)` such that the corresponding audio file can be played when the `speak()` method is called with that particular text:

```java
File audioFile = new File(audioFilePathName)
mTextToSpeech.addSpeech(speechText, audioFilePathName)
mTextToSpeech.speak(speechText, TextToSpeech.QUEUE_ADD, null)
```

The `addSpeech()` method associates the given text string with the audio file. If that audio file doesn't exist, the `speak()` will just synthesize the speech for the text and speak it.

## At the end of using TextToSpeech
When we are done using TextToSpeech, we should call `TextToSpeech.shutdown()` inside the activity's `OnDestroy()` to release all the resources used by its instance so that the TextToSpeech engine can be cleanly stopped and the previous resources held by it can be used by other apps.

## Example App

## References
