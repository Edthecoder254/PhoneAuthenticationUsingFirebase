# PhoneAuthenticationUsingFirebase
Hello guys, This tutorial is going to guide on how to integrate phoneAuthentication into your app.

First we need to create the Phone Authentication Activity. I recommend calling this activity from your Registration Activity in form of a textView i.e. Register Using PhoneNumber
 To create the Activity, Navigate to your Project Tab, Right click on app>New>Activity>EmptyActivity
 Choose an Activity Name and click Finish
 Wait for the gradle to sync.
 
 
 Firebase Authentication
 Firebase is a inified platform supported by google. It offers the following features to mobile developers authentication, Analytics, Storage and Database. There are more services and for more information visit their page at https://firebase.google.com/docs/?authuser=0 
 To use Firebase all you need is a gmail account. If you do not already have one, this is a good time to open an account
 
 Search Firebase Console on your search Engine
 It will prompt you to enter your gmail credentials
 Navigate to Open Add Project and click on it, then Enter Your App Name(Not the Phone Authentication Activity) and nickname then click on Cretae Project
![Add New Project image](../master/myFolder/screenshot14.png) 
![Project Created](../master/myFolder/screenshot15.png) 
 On the page that appears click on the add firebase to your app and choose your the Android icon as shown below
 ![Add Firebase To android](../master/myFolder/screenshot16.png)
 In the next page, Enter your Package name and optionally an app nickame.. It can be found in Android Studio in the Android manifest file
 Important! Ensure You enter the sha certificate number as it is crucial for the app to function
/*SHA generation
In android studio navigate to the gradle tab(far right), then click on the appname(root) dropdown and finally on the android drop down arrow
Next Click on the signing report and wait for the sha certificate to be generated.
Copy and paste the sha1 number in the firebase window
 ![ssh](../master/myFolder/screenshot21.png)
*/

Finally click on create App

Next download the google-Json file and paste it into the studio project files in the android app module root directory
Click Next
Next copy the classpath and dependencies and paste them in your app.Do not forget to add the google-services plugin
click Next and click on the skip this step option 

/*Implementation and classpath
Copy this into your gradle build.gradle(project) file
 dependencies{
classpath 'com.google.gms:google-services:4.0.1'
}
 ![classpath](../master/myFolder/screenshot20.png)
Now Copy this into your build.gradle(app) file
dependencies{
 implementation 'com.google.firebase:firebase-auth:16.0.3'
  implementation 'com.google.firebase:firebase-auth:16.0.3'
 }
// Add this at the bottom 
apply plugin: 'com.google.gms.google-services'
 ![Dependencies](../master/myFolder/screenshot22.png)
Finally sync your project!
*/
Now that your app is set up and ready to go, go back to the firebase console and naviagte to the authentication tab>Sign in method>Phone Authentication and click Enable and finally click Save.


Code
This is the code that will enable phone Authentication in your app
PhoneAuthProvider.getInstance().verifyPhoneNumber(
        phoneNumber,        // Phone number to verify
        60,                 // Timeout duration
        TimeUnit.SECONDS,   // Unit of timeout
        this,               // Activity (for callback binding)
        mCallbacks);        // OnVerificationStateChangedCallbacks
        
        
        Below is the layput file for my main Activity 
        <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">


    <ImageView
        android:id="@+id/imageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="16dp"
        android:background="@drawable/ic_launcher_background" />

    <LinearLayout
        android:id="@+id/linearLayout"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_below="@id/imageView"
        android:orientation="vertical"
        android:padding="15dp">

        <EditText
            android:id="@+id/editTextPhone"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:digits="0123456789"
            android:ems="10"
            android:hint="Enter phone"
            android:inputType="phone" />

        <Button
            android:id="@+id/buttonGetVerificationCode"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Get Verification Code"
            android:textAllCaps="false" />

        <EditText
            android:id="@+id/editTextCode"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:digits="0123456789"
            android:hint="Enter code received"
            android:inputType="number" />

        <Button
            android:id="@+id/buttonSignIn"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Sign In" />
    </LinearLayout>

</RelativeLayout>

Below is the full code for Phoen Authentication using Firebase


import android.content.Intent;
import android.os.Bundle;
import android.support.annotation.NonNull;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.EditText;
import android.widget.Toast;

import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.FirebaseException;
import com.google.firebase.auth.AuthResult;
import com.google.firebase.auth.FirebaseAuth;
import com.google.firebase.auth.FirebaseAuthInvalidCredentialsException;
import com.google.firebase.auth.PhoneAuthCredential;
import com.google.firebase.auth.PhoneAuthProvider;

import java.util.concurrent.TimeUnit;

public class PhoneAuthentication extends AppCompatActivity  {

    EditText editTextPhone, editTextCode;

    FirebaseAuth mAuth;

    String codeSent;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mAuth = FirebaseAuth.getInstance();

        editTextCode = findViewById(R.id.editTextCode);
        editTextPhone = findViewById(R.id.editTextPhone);

        findViewById(R.id.buttonGetVerificationCode).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                sendVerificationCode();
            }
        });


        findViewById(R.id.buttonSignIn).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

                verifySignInCode();
            }
        });
    }

    private void verifySignInCode(){
        String code = editTextCode.getText().toString();
        PhoneAuthCredential credential = PhoneAuthProvider.getCredential(codeSent, code);
        signInWithPhoneAuthCredential(credential);
    }

    private void signInWithPhoneAuthCredential(PhoneAuthCredential credential) {
        mAuth.signInWithCredential(credential)
                .addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {
                    @Override
                    public void onComplete(@NonNull Task<AuthResult> task) {
                        if (task.isSuccessful()) {
                            //here you can open new activity

                            startActivity(new Intent(getApplicationContext(), Profile.class));
                        } else {
                            if (task.getException() instanceof FirebaseAuthInvalidCredentialsException) {
                                Toast.makeText(getApplicationContext(),
                                        "Incorrect Verification Code ", Toast.LENGTH_SHORT).show();
                            }
                        }
                    }
                });
    }

    private void sendVerificationCode(){

        String phone = editTextPhone.getText().toString();

        if(phone.isEmpty()){
            editTextPhone.setError("Phone number is required");
            editTextPhone.requestFocus();
            return;
        }

        if(phone.length() < 10 ){
            editTextPhone.setError("Please enter a valid phone");
            editTextPhone.requestFocus();
            return;
        }


        PhoneAuthProvider.getInstance().verifyPhoneNumber(
                phone,        // Phone number to verify
                60,                 // Timeout duration
                TimeUnit.SECONDS,   // Unit of timeout
                this,               // Activity (for callback binding)
                mCallbacks);        // OnVerificationStateChangedCallbacks
    }



    PhoneAuthProvider.OnVerificationStateChangedCallbacks mCallbacks = new PhoneAuthProvider.OnVerificationStateChangedCallbacks() {

        @Override
        public void onVerificationCompleted(PhoneAuthCredential phoneAuthCredential) {
            // Toast.makeText(getApplicationContext(), "You have been Registered",Toast.LENGTH_LONG).show();

        }

        @Override
        public void onVerificationFailed(FirebaseException e) {

            //Toast.makeText(getApplicationContext(), "Something Failed, Ensure You have Internet Connectivity and Try Again", Toast.LENGTH_SHORT).show();

        }

        @Override
        public void onCodeSent(String s, PhoneAuthProvider.ForceResendingToken forceResendingToken) {
            super.onCodeSent(s, forceResendingToken);

            codeSent = s;
        }
    };


}


