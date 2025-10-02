## Introduction ##
- This repository contains the source code for a sample Jakarta enabled PortletMVC4Spring that can be run in Liferay DXP 2025.Q3.2
- The **Steps Followed to Setup the Module** section contains the steps taken to generate and update the blade tool generated widget to work.

## Prerequisites ##
- JDK 21
- Blade version (7.0.5 currently). Use blade update to get the latest version.
  - If using Windows and an error occurs trying to run 'blade update' then try installing the latest Liferay Dev Studio (currently 3.10.4) which should include the latest Blade version.
- Liferay DXP 2025.Q3.2

## Steps Followed to Setup the Module ##
- Create a new Liferay Workspace using dxp-2025.q3.2
```
blade init -v dxp-2025.q3.2 workspace
```
- Create a new module (from within the workspace folder) using --framework portletmvc4spring
```
blade create -t spring-mvc-portlet --framework portletmvc4spring --view-type jsp -v dxp-2025.q3.2 -p com.mw.springmvc -c TestSetupPortlet com-mw-springmvc
```
- Due to a pending fix in Liferay DXP 2025.Q3.x (expected to be 2025.Q3.3) it is necessary to temporarily remove ALL references to the functions taglib following in .jsp and .jspx files e.g.:
```
xmlns:fn="http://java.sun.com/jsp/jstl/functions"
xmlns:fn="jakarta.tags.functions"
fn:escapeXml
```
- Update build.gradle file:
  - Change the springVersion and springSecurityVersion versions to be:
```
String springVersion = "6.2.10"
String springSecurityVersion = "6.5.3"
```
  - Change hibernate validator by replacing:
```
implementation(group: "com.liferay", name: "org.hibernate.validator", version: "6.2.5.Final.JAKARTA-LIFERAY-PATCHED-1") {
```
with:
```
implementation(group: "org.hibernate.validator", name: "hibernate-validator", version: "8.0.2.Final") {
```
  - Some of the Jakarta gradle dependencies are not yet available in Maven Central, meaning the following must be temporarily included:
    - Update buildscript > repositories section to include the following:
```
maven {
  name = "liferay-third-party"
  url = uri("https://repository.liferay.com/nexus/content/repositories/third-party")
}
mavenCentral()
```
  - Add the following repositories component before the dependencies component:
```
repositories {
    maven {
        url "https://repository-cdn.liferay.com/nexus/content/groups/public"
    }
    maven {
        name = "liferay-third-party"
        url = uri("https://repository.liferay.com/nexus/content/repositories/third-party")
    }
    mavenCentral()
}
```
- Build the module (from within workspace/modules/com-mw-springmvc)
```
..\..\gradlew clean build
```
- Deploy the module to a running Liferay DXP 2025.Q3.2 and check the Liferay logs to ensure the module deployed and started as expected.
- Add the Sample > com-mw-springmvc widget to a page and confirm it works as expected.
