# FirebaseAuth
--
대부분의 앱에서 사용자의 신원 정보를 필요로 합니다. 사용자의 신원을 알면 앱이 사용자 데이터를 클라우드에 안전하게 저장할 수 있고 사용자의 모든 기기에서 개인에게 맞춘 동일한 경험을 제공할 수 있기 때문입니다.

Firebase 인증은 앱의 사용자 인증에 필요한 백엔드 서비스와 사용하기 쉬운 SDK, 기성 UI 라이브러리를 제공합니다. Firebase는 비밀번호 또는 인기 있는 ID 제공업체 (예: Google, Facebook, Twitter 등)를 통한 인증을 지원합니다.

Firebase 인증은 여타 Firebase 서비스와 긴밀히 연동하고 OAuth 2.0 및 OpenId Connect 등의 산업 표준을 활용하므로 맞춤 백엔드와 쉽게 통합할 수 있습니다.

##구현경로
1. 로그인 방법 설정
> 이메일 주소 및 비밀번호를 통한 로그인과 지원하고자 하는 제휴 ID 공급업체를 통한 로그인을 Firebase 콘솔에서 사용 설정합니다

2. 로그인 UI 맞춤설정
> FirebaseUI 옵션을 설정하여 로그인 UI를 맞춤설정하거나 GitHub의 코드를 퍼 와서 로그인 경험을 한층 더 맞춤설정 할 수 있습니다

3. FirebaseUI를 통한 로그인 흐름 진행
> FirebaseUI 라이브러리를 가져와서 지원하고자 하는 로그인 방법을 지정하고 FirebaseUI로그인 흐름을 시작합니다

##Android에서 비밀번호 기반 계정으로 Firebase에 인증하기
1. Android 프로젝트에 Firebase를 추가
2. 앱 수준 build.gradle 파일에 Firebase 인증에 대한 종속 항목을 추가

	> compile 'com.google.firebase:firebase-auth:10.0.1'

3. 아직 Firebase 프로젝트 앱을 연결하지 않았다면 Firebase콘솔에서 연결합니다

4. 다음과 같이 이메일 및 비밀번호 로그인 방법을 사용 설정합니다
	
	a. Firebase 논솔에서 **인증** 섹션을 엽니다 
	
	b. **로그인 방법** 탭에서 **이메일/비밀번호** 로그인 방법을 사용 설저앟고 **저장**을 클릭
	
##비밀번호 기반 계정 생성하기
비밀번호가 있는 신규 사용자 계정을 생성하려면 앱의 로그인 작업에서 다음 절차를 완료합니다


1.다음과 같이 로그인 작업의 **onCreate** 메소드에서 **FirebaseAuth** 개체의 공유 인스턴스를 가져옵니다 


```java
verbatim 69458c03986c585598dcf97060ca7ee3 private FirebaseAuth mAuth; endverbatim 69458c03986c585598dcf97060ca7ee3 
// ...
 verbatim f26bc66839a15cd8d40ebf3c9de1f031 mAuth = FirebaseAuth.getInstance(); endverbatim f26bc66839a15cd8d40ebf3c9de1f031 
```


2.사용자의 로그인 상태 변화에 응답하는 AuthStateListener를 설정합니다.


```java
verbatim 1838d6d325e04b143330f8a7018182b6 private FirebaseAuth.AuthStateListener mAuthListener; endverbatim 1838d6d325e04b143330f8a7018182b6 

// ...

@Override
protected void onCreate(Bundle savedInstanceState) {
    // ...
 verbatim 5454ba2482913c48b3e75b4cbf106037     mAuthListener = new FirebaseAuth.AuthStateListener() {
        @Override
        public void onAuthStateChanged(@NonNull FirebaseAuth firebaseAuth) {
            FirebaseUser user = firebaseAuth.getCurrentUser();
            if (user != null) {
                // User is signed in
                Log.d(TAG, "onAuthStateChanged:signed_in:" + user.getUid());
            } else {
                // User is signed out
                Log.d(TAG, "onAuthStateChanged:signed_out");
            }
            // ...
        }
    }; endverbatim 5454ba2482913c48b3e75b4cbf106037 
    // ...
}

 verbatim 9c619571164ac208e64b26a9c153377a @Override
public void onStart() {
    super.onStart();
    mAuth.addAuthStateListener(mAuthListener);
} endverbatim 9c619571164ac208e64b26a9c153377a 

 verbatim 7d1e3bfe5b688235a6eaee2487a9e50d @Override
public void onStop() {
    super.onStop();
    if (mAuthListener != null) {
        mAuth.removeAuthStateListener(mAuthListener);
    }
} endverbatim 7d1e3bfe5b688235a6eaee2487a9e50d 
```


3.신규 사용자가 앱의 가입 양식을 사용해 가입하고 나면 필요에 따라 앱에서 계정 유효성 검사 절차를 완료합니다. 검사 항목의 예로는 신규 계정의 비밀번호를 정확하게 입력했는지, 비밀번호가 복잡성 조건을 충족하는지 등이 있습니다.


4.다음과 같이 신규 사용자의 이메일 주소와 비밀번호를 createUserWithEmailAndPassword에 전달하여 신규 계정을 생성합니다

```java
verbatim 1de0da447910d6647a9afb8d4735bf38 mAuth.createUserWithEmailAndPassword(email, password)
        .addOnCompleteListener(this, new OnCompleteListener<AuthResult>() {
            @Override
            public void onComplete(@NonNull Task<AuthResult> task) {
                Log.d(TAG, "createUserWithEmail:onComplete:" + task.isSuccessful());

                // If sign in fails, display a message to the user. If sign in succeeds
                // the auth state listener will be notified and logic to handle the
                // signed in user can be handled in the listener.
                if (!task.isSuccessful()) {
                    Toast.makeText(EmailPasswordActivity.this, R.string.auth_failed,
                            Toast.LENGTH_SHORT).show();
                }

                // ...
            }
        }); endverbatim 1de0da447910d6647a9afb8d4735bf38 
```