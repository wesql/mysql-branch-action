# MySQL Branch Action

![Database Icon](https://img.icons8.com/ios-filled/50/000000/database.png)

**Automate MySQL Branch in Your CI/CD Pipelines**

## Table of Contents

- [Introduction](#introduction)
- [Example Workflow](#example-workflow)
- [Features](#features)
- [Inputs](#inputs)
- [Secrets](#secrets)
- [Usage](#usage)
- [Further Reading](#further-reading)
- [License](#license)

## Introduction

The **MySQL Branch Action** is an open-source GitHub Action designed to streamline the creation and management of MySQL database branches within your CI/CD pipelines. By leveraging [WeScale](https://github.com/wesql/wescale/blob/main/doc/toturial/08-Branch.md), this action enables you to create isolated database environments for testing, development, and deployment without impacting your production data. Similar to platforms like Neon and PlanetScale, this solution offers scalable and efficient database branching to enhance your development workflow.

## Example Workflow

Integrate this action into your GitHub Actions workflow to automate the creation of MySQL database branches. This setup ensures isolated environments for testing and development, enhancing your CI/CD pipeline's reliability and efficiency.

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

- **Jobs**:
    - **Create MySQL Branch**: Sets up a MySQL branch with the same schema as the source database, creating an isolated environment for testing and development.
    - **Do Your Schema Migration**: You can perform schema migrations in the branch environment, without affecting the source database.
    - **Branch Diff**: Compares differences between the source and branch databases.
    - **Branch Prepare Merge Back**: Generates DDL statements to merge the branch changes back into the source database.
    - **Branch Merge Back**: Actually merges the branch changes back into the source database.
    - **Branch Show**: Displays the current state of the branches.

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

## Further Reading

For detailed information on managing branches with WeScale, refer to the [WeScale Branch Tutorial](https://github.com/wesql/wescale/blob/main/doc/toturial/08-Branch.md).

## License

This project is licensed under the [MIT License](LICENSE).
