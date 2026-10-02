# Setting up Angular on Local Machine

Use npx to Run a Specific Angular CLI Version Without Installing Globally

```bash

    # check the version of Angular CLI
    npx @angular/cli --version
    npm show @angular/cli versions --json

    npm install @angular/cli@15 --save-dev
    npx ng version

    npx @angular/cli@15 new my-angular15-app
    npx @angular/cli@16 new my-angular16-app

    npx ng serve --port=4500
```

### What is **npx**?

**npx** is a **Node.js package runner** that comes with **npm (v5.2.0 and later)**.

It allows you to **run Node.js packages without installing them globally**.

#### Why npx is useful

- 🚀 Run packages **on demand**
- ❌ No global installation required
- 🔁 Always runs the **latest version** (unless specified)
- 🧪 Great for **one-time tools** and **CLI utilities**

#### Example

```bash
npx create-react-app my-app
```

✔ Runs `create-react-app` without installing it globally.

Other examples:

```bash
npx ng new my-angular-app
npx vite
npx eslint .
```

## New Angular Projects (>= v21)

- Create Angular App in general - no matter the version.

  ```bash
  ng new first-angular-app --no-zoneless.
  ```

- I just run ng new first-angular-app - but with Angular v21+, this will give you a project that is configured in a "zoneless" mode
