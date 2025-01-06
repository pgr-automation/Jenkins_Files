# Jenkins Pipeline with All Types of Parameters

This document provides an example of a Jenkins Declarative Pipeline that demonstrates the use of various parameter types. These parameters allow users to customize the execution of their pipelines.

## Example: Declarative Pipeline
```groovy
pipeline {
    agent any

    parameters {
        // String Parameter
        string(name: 'STRING_PARAM', defaultValue: 'defaultString', description: 'This is a string parameter')

        // Boolean Parameter
        booleanParam(name: 'BOOLEAN_PARAM', defaultValue: true, description: 'This is a boolean parameter')

        // Choice Parameter
        choice(name: 'CHOICE_PARAM', choices: ['Option1', 'Option2', 'Option3'], description: 'This is a choice parameter')

        // Text Parameter
        text(name: 'TEXT_PARAM', defaultValue: 'Default text', description: 'This is a text parameter')

        // Password Parameter
        password(name: 'PASSWORD_PARAM', description: 'This is a password parameter')

        // File Parameter
        file(name: 'FILE_PARAM', description: 'This is a file parameter')

        // Run Parameter
        run(name: 'RUN_PARAM', description: 'Select a specific run of another job', job: 'example-job-name')

        // Credentials Parameter (requires credentials plugin)
        credentials(name: 'CREDENTIALS_PARAM', description: 'This is a credentials parameter')

        // Multi-line String Parameter (requires a plugin, such as "Extended Choice Parameter")
        extendedChoice(name: 'MULTI_STRING_PARAM', description: 'Multi-line string parameter', type: 'Textarea', value: 'Line1\nLine2\nLine3')

        // Active Choices Parameter (requires Active Choices Plugin)
        activeChoiceParam(name: 'ACTIVE_CHOICE_PARAM') {
            description('This is an active choice parameter')
            script {
                return ['Dynamic Option 1', 'Dynamic Option 2', 'Dynamic Option 3']
            }
        }
    }

    stages {
        stage('Display Parameters') {
            steps {
                script {
                    echo "String Parameter: ${params.STRING_PARAM}"
                    echo "Boolean Parameter: ${params.BOOLEAN_PARAM}"
                    echo "Choice Parameter: ${params.CHOICE_PARAM}"
                    echo "Text Parameter: ${params.TEXT_PARAM}"
                    echo "Password Parameter: ${params.PASSWORD_PARAM}"
                    echo "File Parameter: ${params.FILE_PARAM}" // This will be a path to the uploaded file
                    echo "Run Parameter: ${params.RUN_PARAM}"
                    echo "Credentials Parameter: ${params.CREDENTIALS_PARAM}"
                }
            }
        }
    }
}
```

## Explanation of Parameter Types

### 1. **String Parameter**
- Accepts a single-line text input.

### 2. **Boolean Parameter**
- Provides a checkbox for true/false input.

### 3. **Choice Parameter**
- Offers a dropdown menu with pre-defined options.

### 4. **Text Parameter**
- Accepts multi-line text input.

### 5. **Password Parameter**
- Securely accepts sensitive text input.

### 6. **File Parameter**
- Allows users to upload files.

### 7. **Run Parameter**
- Links to a specific build of another job.

### 8. **Credentials Parameter**
- Securely selects credentials stored in Jenkins.

### 9. **Active Choices Parameter**
- Dynamically populates options based on a Groovy script (requires Active Choices Plugin).

### 10. **Multi-line String Parameter**
- Accepts multi-line text (requires Extended Choice Parameter plugin).

## Notes

- Some parameters, such as `extendedChoice` or `activeChoiceParam`, require specific plugins.
- Sensitive data like passwords or credentials should always be handled securely and only used in secure contexts.
- For file parameters, the uploaded file will be available in the workspace directory.

## Customization

You can modify this pipeline example to fit your specific requirements. Let us know if you need assistance customizing it!
