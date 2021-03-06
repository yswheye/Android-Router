# Android-Router
English | [中文](https://github.com/TangXiaoLv/Android-Router/blob/master/README_CN.md)

<img src="img/2.png" width = "660" height = "300"/>

|lib|androidrouter|androidrouter-compiler|androidrouter-annotations|
|---|---|---|---|
|version|[ ![Download](https://api.bintray.com/packages/tangxiaolv/maven/androidrouter/images/download.svg?version=1.0.1) ](https://bintray.com/tangxiaolv/maven/androidrouter/1.0.1/link)|[ ![Download](https://api.bintray.com/packages/tangxiaolv/maven/androidrouter-compiler/images/download.svg?version=1.0.0) ](https://bintray.com/tangxiaolv/maven/androidrouter-compiler/1.0.0/link)|[ ![Download](https://api.bintray.com/packages/tangxiaolv/maven/androidrouter-annotations/images/download.svg?version=1.0.0) ](https://bintray.com/tangxiaolv/maven/androidrouter-annotations/1.0.0/link)|
High-performance, flexible, easy-to-use lightweight Android component-based framework, Used to solve the interdependence of complex projects, A single module is conducive to independent development and maintenance.

Goal
---
- Project decoupling
- Stand-alone develop,Stand-alone maintenance.
- Make life better

Characteristics
---
- Uses annotation processing to generate boilerplate code for you.
- Exception centralized processing.
- Any parameter type return.
- Runtime parameter parse，Support different types of delivery.
    * From jsonObject => To Object
    * From jsonArray => To List
    * From Object A => To Object A
    * From Object A => To Object B
    * From List< A> => To Object List< B> 

Modular architecture diagram
---

<img src="img/1.png" width = "824" height = "528"/>

Gradle
---
```
//Add dependencies inside application/library.
dependencies {
    compile 'com.library.tangxiaolv:androidrouter:1.0.1'
    annotationProcessor 'com.library.tangxiaolv:androidrouter-compiler:1.0.0
}
```

Getting Started
---
Note：Android-Router 
Protocol Format: scheme://host/path?params=json
*scheme[1] host[1] path[2] params[2] 1:required 2:option*

###Step 1:Setup router module.
```java
/**
 * Supported parameter types
 *
 * float
 * int
 * long
 * double
 * boolean
 * String
 * List<?>
 * Map<String,Object>
 * custom object
 * 
 * default parameter: Application context, String scheme, VPromise promise
 */
@RouterModule(scheme = "android", host = "main")
public class MainModule implements IRouter {


    //Route => android://main
    @RouterPath
    public void def(Application context, String scheme, VPromise promise) {
        promise.resolve("","from scheme: [" + scheme + "] " + "path: []");
    }

    //Route => android://main/activity/localActivity
    @RouterPath("/activity/localActivity")
    public void openLocalActivityAndReturnResult(Application context, VPromise promise) {
        String tag = promise.getTag();
        Intent intent = new Intent(context, LocalActivity.class);
        intent.putExtra("tag", tag);
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(intent);
    }

    //Take out the value from json object
    //Route => android://main/params/basis?params={'f':1,'i':2,'l':3,'d':4,'b':true}
    @RouterPath("/params/basis")
    public void paramsBasis(float f, int i, long l, double d, boolean b,
                            String scheme, VPromise promise) {
        promise.resolve("","from scheme: [" + scheme + "] " + "path: [/params/basis]");
    }

    //Take out the complex value from json object
    //Route => android://main/params/complex?params={'b':{},'listC':[]}
    @RouterPath("/params/complex")
    public void paramsComplex(B b, List<C> listC, String scheme, VPromise promise) {
        promise.resolve("","from scheme: [" + scheme + "] " + "path: [/params/complex]");
    }

    //From json object => to object
    //The key must be "_params_"
    @RouterPath("/jsonObject")
    public void paramsPakege(Package _params_, String scheme, VPromise promise) {
        promise.resolve("","from scheme: [" + scheme + "] " + "path: [/jsonObject]");
    }

    //From json array => to List
    //The key must be "_params_"
    @RouterPath("/jsonArray")
    public void jsonArray(List<A> _params_, String scheme, VPromise promise) {
        promise.resolve("","from scheme: [" + scheme + "] " + "path: [/jsonArray]");
    }

    //eg: from A => to B
    //If passed different type of object.The basic params name and type must be the same.
    @RouterPath("/differentTypes")
    public void differentTypes(A a, List<A> listA, String scheme, VPromise promise) {
        promise.resolve("","from scheme: [" + scheme + "] " + "path: [/differentTypes]");
    }

    //If you want to return an error.Recommend use RouterRemoteException
    @RouterPath("/throwError")
    public void throwError(VPromise promise) {
        promise.reject(new RouterRemoteException("I'm error................."));
    }
}

//Support multiple scheme.
@RouterModule(scheme = "android|remote", host = "lib")
public class RemoteModule implements IRouter {

    @RouterPath("/openRemoteActivity")
    public void openRemoteActivity(Application context, String scheme, VPromise promise) {
        Intent intent = new Intent(context, RemoteActivity.class);
        intent.putExtra("tag", promise.getTag());
        intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
        context.startActivity(intent);
    }
}
```
###Step 2:Invoke
```
AndroidRouter.open("android://main/activity/localActivity").call(new Resolve() {
        @Override
        public void call(String type, Object result) {
            //Receive result
        }
    }, new Reject() {
        @Override
        public void call(Exception e) {
            //All routing errors are callback here.
        }
    });

//or
AndroidRouter.open("android", "main", "/differentTypes", null)
    .showTime()//Show time
    .call();//Igone result and error.
```
###Proguard
```
//Add proguard-rules
-keep class * implements com.tangxiaolv.router.interfaces.IMirror{*;}
-keep class * implements com.tangxiaolv.router.interfaces.IRouter{*;}
```
License
---
    Copyright 2017 TangXiaoLv
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
        http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
