
node {

  stage 'Checkout'
    checkout scm

  stage 'Creating zip'
    sh "zip helloworld.zip helloworld.js"

  stage 'Updating Lambda and Creating new Version'
    version = sh(returnStdout: true, script: "aws lambda update-function-code --function-name helloworld --zip-file fileb://./helloworld.zip --publish --region us-west-2 --query 'Version' --output text")

  stage 'Deploy to Test'
    sh "aws lambda update-alias --function-name helloworld --name TEST --region us-west-2 --function-version ${version} "

  stage 'Test via Invokation'
    result = sh(returnStdout: true, script: "aws lambda invoke --function-name helloworld:TEST --payload file://./test.json --region us-west-2 output.txt")
    if (!result.contains("200")) {
       error: "Error in Lambda Invokation"
    }

  stage 'Promote to Prod'
     sh "aws lambda update-alias --function-name helloworld --name PROD --function-version ${version} --region us-west-2"
     
}
