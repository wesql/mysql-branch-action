# MySQL Branch Creation Action

![Database Icon](https://img.icons8.com/ios-filled/50/000000/database.png)

**Creates a MySQL branch using WeScale**

## Table of Contents

- [Usage](#usage)
- [Inputs](#inputs)
- [Secrets](#secrets)
- [Example Workflow](#example-workflow)
- [License](#license)

## Usage

Use this GitHub Action to set up a MySQL branch in your CI/CD pipeline. It leverages WeScale to manage MySQL instances efficiently.

## Inputs

| Name               | Description                                 | Required | Default                                |
| ------------------ | ------------------------------------------- | -------- | -------------------------------------- |
| `source_host`      | Source host address                         | Yes      | N/A                                    |
| `source_port`      | Source port                                 | No       | `3306`                                 |
| `source_user`      | Source user                                 | No       | `root`                                 |
| `include_databases`| Databases to include                        | No       | `*`                                    |
| `exclude_databases`| Databases to exclude                        | No       | `information_schema,mysql,performance_schema,sys` |
| `wescale_image`    | WeScale image tag                           | No       | `apecloud/apecloud-mysql-scale:0.3.8`  |

## Secrets

To handle sensitive information securely, this action uses GitHub Secrets. You need to define the following secret in your repository:

| Secret Name       | Description                          |
| ----------------- | ------------------------------------ |
| `source_password` | Source password for MySQL            |

### Setting Up Secrets

1. **Navigate to Your Repository**:
    - Go to your GitHub repository.

2. **Access Settings**:
    - Click on the `Settings` tab.

3. **Add Secrets**:
    - In the sidebar, click on `Secrets and variables` > `Actions`.
    - Click the `New repository secret` button.

4. **Define Secret**:
    - **Name**: Enter `source_password`.
    - **Value**: Enter your MySQL source password.
    - Click `Add secret`.

## Example Workflow

Below is an example of how to integrate the **MySQL Branch Creation Action** into your GitHub Actions workflow.

```yaml
name: Setup MySQL Branch

on:
  push:
    branches:
      - main

jobs:
  setup-mysql:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Create MySQL Branch
        uses: wesql/mysql-branch-creation-action@v1
        with:
          source_host: 'your-source-host.com'
          source_port: '3306' # Optional
          source_user: 'root' # Optional
          include_databases: '*' # Optional
          exclude_databases: 'information_schema,mysql,performance_schema,sys' # Optional
          wescale_image: 'apecloud/apecloud-mysql-scale:0.3.8' # Optional
        secrets:
          source_password: ${{ secrets.source_password }}
```

### Explanation

- **Trigger**: The workflow is triggered on every push to the `main` branch.
- **Job**:
    - **Checkout Repository**: Uses the `actions/checkout` action to clone the repository.
    - **Create MySQL Branch**: Utilizes the `MySQL Branch Creation Action` with required inputs and secrets.
        - **Inputs**:
            - `source_host`: The address of your source MySQL server.
            - `source_port`: The port of your source MySQL server (optional, default is `3306`).
            - `source_user`: The username for your source MySQL server (optional, default is `root`).
            - `include_databases`: Databases to include in the branch (optional, default is `*`).
            - `exclude_databases`: Databases to exclude from the branch (optional, default is `information_schema,mysql,performance_schema,sys`).
            - `wescale_image`: The WeScale Docker image tag (optional, default is `apecloud/apecloud-mysql-scale:0.3.8`).
        - **Secrets**:
            - `source_password`: Your source MySQL password, securely accessed via GitHub Secrets.

## License

This project is licensed under the [MIT License](LICENSE).