<h1 align="center">
  Running Cypress tests on BrowserStack
</h1>

## Using Console (CLI)
**Note: Please clone this repository before performing the steps mentioned below**

* Install browserstack's command line tool for cypress:
```node
npm install -g browserstack-cypress-cli
```
* Have a look at your [browserstack.json](./browserstack.json) file
This is a browserstack config file that allows you to pass the runner capabilities (Browser and OS combinations) along with additional capabilities like 'Path to cypress config file', 'Number of BrowserStack Parallels to be used' and so on.
```json
{
    "auth": {
        "username": "<Your BrowserStack username>",
        "access_key": "<Your BrowserStack access key>"
    },
    "browsers": [
        {
            "browser": "chrome",
            "os": "Windows 10",
            "versions": [
                "latest"
            ]
        },
        {
            "browser": "firefox",
            "os": "Windows 10",
            "versions": [
                "latest"
            ]
        },
        {
            "browser": "chrome",
            "os": "OS X Catalina",
            "versions": [
                "latest"
            ]
        },
        {
            "browser": "firefox",
            "os": "OS X Catalina",
            "versions": [
                "latest"
            ]
        }
    ],
    "run_settings": {
        "cypress_config_file": "./cypress.json",
        "project_name": "YOUR_PROJECT_NAME",
        "build_name": "YOUR_BUILD_NAME",
        "parallels": "5"
    }
}
```
You can choose to hard-code your browserstack credentials in this file. However, we encourage the use of exporting envronment variables (BROWSERSTACK_USERNAME and BROWSERSTACK_ACCESS_KEY) in your command line.
* Run your tests using the browserstack command line tool:
```node
browserstack-cypress run --sync
```

## Using GitHub Actions
**Note: Please fork this repository before performing the steps mentioned below**

* Set up GitHub secrets in your repository
You need to have username and access-key from BrowserStack to be able to run tests on the BrowserStack infrastructure.
![image](https://user-images.githubusercontent.com/46998848/142492969-889af9f3-abce-478d-8453-15da037715f5.png)

Go to **Settings** of your repository, select **Secrets** and then click on **New Secret** as shown below:
![image](https://user-images.githubusercontent.com/46998848/142493235-ff84ef08-6b74-44cb-80e7-70c95e254620.png)

Add YOUR_USERNAME as the BROWSERSTACK_USERNAME secret and similarly, add YOUR_ACCESS_KEY as the BROWSERSTACK_ACCESS_KEY secret. After these two steps, the secrets page should contain entries like this:
![image](https://user-images.githubusercontent.com/46998848/142493309-cc19c42f-fa74-41ee-bea3-63463e95f93e.png)

* You can view and edit the existing GitHub Actions workflow in [browserstack.yml](.github/workflows)
```yaml
name: 'BrowserStack Test'
on: [push, pull_request]
jobs:
  ubuntu-job:
    name: 'BrowserStack Test on Ubuntu'
    runs-on: ubuntu-latest  # Can be self-hosted runner also
    steps:

      - name: 'BrowserStack Env Setup'  # Invokes the setup-env action
        uses: browserstack/github-actions/setup-env@master
        with:
          username:  ${{ secrets.BROWSERSTACK_USERNAME }}
          access-key: ${{ secrets.BROWSERSTACK_ACCESS_KEY }}

# The next 3 steps are for building the web application to be tested and starting the web server on the runner environment

      - name: 'Checkout the repository'
        uses: actions/checkout@v2

      - name: 'Installing all the dependencies'
        run: npm install 

      - name: 'Installing browserstack-cypress-cli'
        run: npm install -g browserstack-cypress-cli

      - name: 'Running test on BrowserStack'  # Invokes the actual test script that would run on BrowserStack browsers
        run: browserstack-cypress run --sync  # See sample test script above
```
And that's about it! <br>
**You can go ahead and make a push or create a pull request on your forked repository and see your cypress tests in action**

## References:
* [Cypress - Automate](https://www.browserstack.com/docs/automate/cypress)
* [GitHub Actions with BrowserStack](https://www.browserstack.com/docs/automate/selenium/github-actions)
