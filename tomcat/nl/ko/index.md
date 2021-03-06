---

copyright:
  years: 2015, 2018
lastupdated: "2018-09-05"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}


# Tomcat
{: #tomcat_runtime}

{{site.data.keyword.Bluemix}}의 Tomcat 런타임은 java_buildpack을 통해 제공됩니다.
{: shortdesc}

{{site.data.keyword.Bluemix_notm}}에서 Tomcat 런타임을 사용하려면 java_buildpack을 `-b` 옵션으로 지정해야 합니다. 예를 들어, 다음과 같습니다.

```
ibmcloud cf push <myApp> -p <pathToMyApp> -b java_buildpack
```

Tomcat 런타임에 대한 자세한 정보는
[java-buildpack readme](https://github.com/cloudfoundry/java-buildpack/blob/master/README.md)를 참조하십시오.

## 스타터 애플리케이션
{: #starter_application}

{{site.data.keyword.Bluemix_notm}}는 Tomcat 스타터 애플리케이션을 제공합니다.  Tomcat 스타터 애플리케이션은 사용할 수 있는 템플리트를 제공하는 단순한 Tomcat 앱입니다. 스타터 앱을 사용하여 시험해 볼 수 있으며 {{site.data.keyword.Bluemix_notm}} 환경을 변경하고 변경사항을 푸시할 수
있습니다. 스타터 애플리케이션 사용에 대한 도움말은 [스타터 애플리케이션 사용](../common/starter_app_usage.html)을 참조하십시오.

## 런타임 버전
{: #runtime_versions}

JBP_CONFIG_TOMCAT 환경 변수를 사용하여 앱에서 사용할 Tomcat 버전을 변경할 수 있습니다.
JBP_CONFIG_OPEN_JDK_JRE 환경 변수를 사용하여 앱에서 사용할 Java 버전을 변경할 수 있습니다.
두 환경 변수 모두 애플리케이션의 Manifest 파일에서 지정할 수 있습니다.  예를 들어, 다음과 같습니다.
```
    env:
        JBP_CONFIG_TOMCAT: '{tomcat: { version: 8.5.+ }}'
        JBP_CONFIG_OPEN_JDK_JRE: '{jre: { version: 1.8.0_+ }}'
```
{: codeblock}
현재 java_buildpack 버전은 기본 Tomcat 버전 8.5.28 및 기본 Java 버전 1.8.0_162을 포함하는 v4.9입니다.
자세한 정보는 [java-buildpack 릴리스](https://github.com/cloudfoundry/java-buildpack/releases/tag/v4.9)를 참조하십시오.

## HTTPS 경로 재지정
{: #https_redirect}

{{site.data.keyword.Bluemix_notm}} 내부 프록시를 신뢰하고 HTTP 트래픽을 HTTPS(SSL)로 경로를 재지정할 수 있도록 Tomcat 런타임을 구성할 수 있습니다.
이 작업을 수행하려면 `server.xml` 파일을 수정하여 `RemoteIpValve` Valve 요소를 `internalProxies` 및 `protocolHeader` 옵션으로 설정하십시오.

빌드팩에 포함된 Tomcat 런타임 [server.xml](https://github.com/cloudfoundry/java-buildpack/blob/master/resources/tomcat/conf/server.xml)은 기본적으로 `RemoteIpValve` Valve 요소의 `protocolHeader`만 설정합니다.  {{site.data.keyword.Bluemix_notm}}에서 HTTP 트래픽의 경로를 HTTPS로 재지정하려면 다음 예제에 표시된 대로 사용자 정의 `server.xml`에서 `RemoteIpValve` 요소를 구성하십시오.

```
 <Valve className='org.apache.catalina.valves.RemoteIpValve' protocolHeader='x-forwarded-proto' internalProxies='.*' />
```
{: codeblock}

RemoteIpValve에 대한 추가 구성 옵션은
[Tomcat 문서 ![외부 링크 아이콘](../../icons/launch-glyph.svg "외부 링크 아이콘")](https://tomcat.apache.org/tomcat-8.5-doc/api/org/apache/catalina/valves/RemoteIpValve.html)를 참조하십시오.
