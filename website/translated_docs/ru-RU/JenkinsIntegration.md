---
id: jenkins
title: Интеграция с Jenkins
---

WebdriverIO может работать с системами непрерывной интеграции (CI), такими как [Jenkins](https://jenkins-ci.org/). В этом примере для отладки тестов и отслеживания их результатов рассмотрим [junit reporter](https://github.com/webdriverio/wdio-junit-repo). Сам процесс интеграции довольно прост.

Во-первых, нам необходимо назначить `junit` в качестве репортера. Так же, стоит убедиться, что репортер установлен (`$ npm install --save-dev wdio-junit-reporter`) и что место, куда мы будем сохранять результаты тестов, доступно для Jenkins'а. Следовательно, назначаем репортер в файле конфигурации:

```js
// wdio.conf.js
module.exports = {
    // ...
    reporters: [
        'dot',
        ['junit', {
            outputDir: './'
        }]
    ],
    // ...
};
```

It is up to you which framework you want to choose. The reports will be similar. This tutorial is going to use Jasmine. After you have written couple of tests you can begin to setup a new Jenkins job. Give it a name and a description:

![Name And Description](/img/jenkins/jobname.png "Name And Description")

Then make sure it grabs always the newest version of your repository:

![Jenkins Git Setup](/img/jenkins/gitsetup.png "Jenkins Git Setup")

Now the important part: create a build step to execute shell commands. That build step needs to build your project. Since this demo project only tests an external app we don't need to build anything but install the node dependencies and run our test command `npm test` which is an alias for `node_modules/.bin/wdio test/wdio.conf.js`.

![Build Step](/img/jenkins/runjob.png "Build Step")

After our test we want Jenkins to track our xunit report. To do so we have to add a post-build action called *"Publish JUnit test result report"*. You could also install an external xunit plugin to track your reports. The JUnit one comes with the basic Jenkins installation and is sufficient enough for now.

According to our config file we store the xunit reports in our workspace root directory. These reports are xml files. So all we need to do in order to track the reports is to point Jenkins to all xml files in our root directory:

![Post-build Action](/img/jenkins/postjob.png "Post-build Action")

That's it! This is all you need to setup Jenkins to run your WebdriverIO jobs. Your job will now provide detailed test results with history charts, stacktrace information on failed jobs as well as a list of commands with payload that got used in each test.

![Jenkins Final Integration](/img/jenkins/final.png "Jenkins Final Integration")