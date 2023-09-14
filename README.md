# Project setup

## Prerequisites
- Up-to-date Node and npm tools
- Installed Ionic CLI (`npm install -g @ionic/cli@latest`)
- Installed all required dependencies in `package.json` (`npm install` when in project folder)

## Run the project
1. Open the project in VSCode
2. Run `npx cap add android; npx cap add ios` to add support for both native platforms
3. When editing code, make sure to run `ionic build; ionic cap copy; ionic cap sync`
4. To open specific platform `npx cap open android` or `npx cap open ios`
5. When specific platform project is opened, you can treat it as standard native project so you can directly hit run and test it on the simulator
