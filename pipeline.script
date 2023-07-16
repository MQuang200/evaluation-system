pipeline {
  agent {
    kubernetes {
      yaml '''
        apiVersion: v1
        kind: Pod
        spec:
          volumes:
          - name: data
            nfs:
              server: "10.96.200.2"
              path: "/" # "shared" folder must exist inside "/exports" directory of NFS server
          containers:
          - name: java
            image: openjdk:17
            volumeMounts:
                - name: data
                  mountPath: /mnt/data
            command:
            - cat
            tty: true
        '''
    }
  }
  stages {
    stage('Build stage') {
      steps {
        script {
          env.id = env.BRANCH_NAME.replace("origin/", "").split("-")[0]
          env.email = env.BRANCH_NAME.replace("origin/", "").split("-")[1]
        }
        container('java') {
        //   sh 'ls /mnt/data/artifact'
          sh '''
            ls
            parent_dir="/mnt/data"
            prefix="${id}"

            # Find the maximum existing index
            max_index=$(ls -d $parent_dir/$prefix-* 2>/dev/null | sed "s|$parent_dir/$prefix-||" | sort -nr | head -n1)

            # If no existing index, start from 1, otherwise increment the index
            if [ -z "$max_index" ]; then
                new_index=1
            else
                new_index=$((max_index + 1))
                rm -rf "$parent_dir/$prefix-$max_index"
            fi

            # Create new directory
            directory_name="$parent_dir/$prefix-$new_index"
            mkdir -p "$directory_name/artifact"
            #copy all the source of student to this directory
            cp ./*.java "$directory_name"
            ls /mnt/data/${id}*
            javac -d "$directory_name/artifact" $directory_name/*.java
          '''
//           sh 'java -jar /mnt/data/analyzer-lca-spring-0.0.1-SNAPSHOT.jar'
//           sh 'cp /mnt/data/artifact/Questions.txt .'
//           sh 'ls'
//           sh 'ls /mnt/data/artifact'
        }
      }
//       post {
//             always {
//                 archiveArtifacts 'Questions.txt'
//             }
//         }
    }
    stage('Test stage') {
      steps {
        container('java') {
          sh '''
            cd /mnt/data/${id}*/artifact && java Lab2/Main 3
          '''
        }
      }
    }
    stage('Analyzer stage') {
      steps {
        container('java') {
          sh '''
            java -jar /mnt/data/analyzer-lca-spring-0.0.1-SNAPSHOT.jar /mnt/data/${id}*
            cp /mnt/data/${id}*/artifact/Questions.txt .
          '''
        }
      }
    }
  }
  post {
        failure {
          script {
            emailext (
                to: env.email,
                subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Check the attached report.",
                attachLog: true,
            )
          }
        }

        success {
          script {
            emailext (
                to: env.email,
                subject: "Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Check the attached questions.",
                attachLog: false,
                attachmentsPattern: "Questions.txt"
            )
          }
        }
    }
}
