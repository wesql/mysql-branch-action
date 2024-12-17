# MySQL Branch Creation Action

![Database Icon](https://img.icons8.com/ios-filled/50/000000/database.png)

**Automate MySQL Branch Creation in Your CI/CD Pipelines**

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Inputs](#inputs)
- [Secrets](#secrets)
- [Usage](#usage)
- [Example Workflow](#example-workflow)
- [Further Reading](#further-reading)
- [License](#license)

## Introduction

The **MySQL Branch Action** is a GitHub Action designed to streamline the creation and management of MySQL database branches within your CI/CD pipelines. By leveraging [WeScale](https://github.com/wesql/wescale/blob/main/doc/toturial/08-Branch.md), this action facilitates efficient database operations, enabling you to create isolated branches for testing, development, and deployment without affecting your production data.

## Features

- **Automated Branch Creation**: Quickly create and manage MySQL database branches.
- **Secure Credentials Management**: Utilizes GitHub Secrets to handle sensitive information securely.
- **Customizable Configuration**: Configure various parameters to fit your project's requirements.
- **Seamless Integration**: Easily integrates into existing GitHub Actions workflows.
- **Scalable Infrastructure**: Leverages WeScale for efficient MySQL instance management.

## Inputs

| Name                | Description                                                   | Required | Default                                       |
| ------------------- | ------------------------------------------------------------- | -------- | --------------------------------------------- |
| `source_host`       | Hostname or IP address of the source MySQL server.            | Yes      | N/A                                           |
| `source_port`       | Port number of the source MySQL server.                       | No       | `3306`                                        |
| `source_user`       | Username for the source MySQL server.                          | No       | `root`                                        |
| `source_password`   | Password for the source MySQL server.                          | Yes      | N/A                                           |
| `include_databases` | Comma-separated list of databases to include in the branch.    | No       | `*`                                           |
| `exclude_databases` | Comma-separated list of databases to exclude from the branch.  | No       | `information_schema,mysql,performance_schema,sys` |
| `wescale_image`     | Docker image tag for WeScale.                                  | No       | `apecloud/apecloud-mysql-scale:0.3.8-alpha5`  |

## Secrets

To securely handle sensitive information, this action uses GitHub Secrets. You need to define the following secret in your repository:

| Secret Name       | Description                          |
| ----------------- | ------------------------------------ |
| `SOURCE_PASSWORD` | Password for the source MySQL server. |

### Setting Up Secrets

1. **Navigate to Your Repository**:
    - Go to your GitHub repository.

2. **Access Settings**:
    - Click on the `Settings` tab.

3. **Add Secrets**:
    - In the sidebar, click on `Secrets and variables` > `Actions`.
    - Click the `New repository secret` button.

4. **Define Secret**:
    - **Name**: Enter `SOURCE_PASSWORD`.
    - **Value**: Enter your source MySQL password.
    - Click `Add secret`.

## Usage

Integrate this action into your GitHub Actions workflow to automate the creation of MySQL database branches. This setup ensures isolated environments for testing and development, enhancing your CI/CD pipeline's reliability and efficiency.

## Example Workflow

Below is an example of how to integrate the **MySQL Branch Creation Action** into your GitHub Actions workflow. This workflow triggers on every push to the `main` branch, sets up the MySQL branch, performs schema migrations, and manages the branch lifecycle.

```yaml
name: Branch Example

on:
  push:

jobs:
  setup-mysql:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Create MySQL Branch
        uses: wesql/mysql-branch-action@v0.0.4
        with:
          source_host: 'your-source-host.com'
          source_port: '3306' # Optional
          source_user: 'your_user' # Optional
          source_password: ${{ secrets.SOURCE_PASSWORD }}
          include_databases: '*' # Optional
          exclude_databases: 'information_schema,mysql,performance_schema,sys' # Optional
          wescale_image: 'apecloud/apecloud-mysql-scale:0.3.8' # Optional

      - name: Do Your Schema Migration
        run: |
          mysql -h127.0.0.1 -P3306 -u root -pYOUR_PASSWORD -e "CREATE DATABASE IF NOT EXISTS foobar;"
          mysql -h127.0.0.1 -P3306 -u root -pYOUR_PASSWORD -e "CREATE TABLE IF NOT EXISTS foobar.account (id INT PRIMARY KEY, name VARCHAR(255));"

      - name: Branch Diff
        run: |
          mysql -h127.0.0.1 -P3306 -u root -pYOUR_PASSWORD -e "Branch diff"

      - name: Branch Prepare Merge Back
        run: |
          mysql -h127.0.0.1 -P3306 -u root -pYOUR_PASSWORD -e "Branch prepare_merge_back"

      - name: Branch Merge Back
        run: |
          mysql -h127.0.0.1 -P3306 -u root -pYOUR_PASSWORD -e "Branch merge_back"

      - name: Branch Show
        run: |
          mysql -h127.0.0.1 -P3306 -u root -pYOUR_PASSWORD -e "Branch show"
```

### Explanation

- **Trigger**: This workflow is triggered on every push to the `main` branch.
- **Jobs**:
    - **Checkout Repository**: Uses the `actions/checkout` action to clone the repository.
    - **Create MySQL Branch**: Utilizes the `MySQL Branch Creation Action` with required inputs and secrets.
        - **Inputs**:
            - `source_host`: The address of your source MySQL server.
            - `source_port`: The port of your source MySQL server (optional, default is `3306`).
            - `source_user`: The username for your source MySQL server (optional, default is `root`).
            - `source_password`: Your source MySQL password, securely accessed via GitHub Secrets.
            - `include_databases`: Databases to include in the branch (optional, default is `*`).
            - `exclude_databases`: Databases to exclude from the branch (optional, default is `information_schema,mysql,performance_schema,sys`).
            - `wescale_image`: The WeScale Docker image tag (optional, default is `apecloud/apecloud-mysql-scale:0.3.8-alpha5`).
    - **Do Your Schema Migration**: Executes SQL commands to create databases and tables as part of your migration process.
    - **Branch Diff**: Compares differences between the source and branch databases.
    - **Branch Prepare Merge Back**: Prepares the branch for merging back into the source.
    - **Branch Merge Back**: Merges the branch changes back into the source database.
    - **Branch Show**: Displays the current state of the branches.

## Further Reading

For detailed information on managing branches with WeScale, refer to the [WeScale Branch Tutorial](https://github.com/wesql/wescale/blob/main/doc/toturial/08-Branch.md).

## License

This project is licensed under the [MIT License](LICENSE).