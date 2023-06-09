//Oleg Milyukov

node {
    stage("Generate Password") {
        
        println("Length of the password: ${password_length}")
        println("Number of attempts: ${attempts_number}")
        
        def password_amount = input(
            id: "userParallelThreads",
            message: "Amount of parallel attempts",
            parameters: [
                string(defaultValue: "1",
                        description: "integer",
                        name: "password_amount")
                ])
        
        def passwordHistoryFile = new File("password_history.txt")
        def passwordHistory = [] 

        if (passwordHistoryFile.exists()) {
            println("Password history file exists.")
            passwordHistory = passwordHistoryFile.readLines()
            println("Password history file")
            println passwordHistoryFile.text
        }
        else {
            passwordHistoryFile.createNewFile()
            passwordHistory = passwordHistoryFile.readLines()
            println("Created password history file.")
        }

        def resultPasswords = []
        def passwordsStrengths = []
        def builders  = [:]

        for (int i = 1; i <= password_amount.toInteger(); i++) {
            def counter = i
            def label = "password_$i"
            builders [label] = {
                def currentPassword = generatePassword(params.password_length.toInteger(), params.attempts_number.toInteger())
                def currentStrength = isPasswordStrong(currentPassword)

                if (!passwordHistory.contains(currentPassword)) {
                    resultPasswords.add(currentPassword)
                    passwordsStrengths.add(currentStrength)
                    passwordHistoryFile.append(currentPassword + "\n")
                }
            }
        }

        parallel(builders)
        for (int i = 0; i < password_amount.toInteger(); i++) {
            println("Generated password: ${resultPasswords[i]}")
            println("Password strength: ${passwordsStrengths[i]}")
        }

        println("Password history file")
        println passwordHistoryFile.text
    }
}

def generatePassword (int passLength, int maxAttempts) {
    def attempts = 1
    while (attempts <= maxAttempts) {
        def password = new StringBuilder()
        echo "attempt #${attempts}"
        for (int i = 1; i <= passLength; i++) {
            def option = (int) (Math.random() * 3)
            switch (option) {
                case 0:
                    password.append((char) ((int) (Math.random() * 26) + 97))
                    break
                case 1:
                    password.append((char) ((int) (Math.random() * 26) + 65))
                    break
                case 2:
                    password.append((char) ((int) (Math.random() * 10) + 48))
                    break
            }
            echo "step ${i}: ${password}"
        }

        if (password.toString().matches("^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9]).*")) {
            return password.toString()
        } else {
            attempts++
        }
    }
    throw new Exception("Could not generate strong password after ${attempts_number} attempts.")
}

def isPasswordStrong (String password) {
    def passwordStrength = ""
    if (password.length() >= 10)
        passwordStrength += "\u2705"
    if (password.matches("^(?=.*[a-z]).*"))
        passwordStrength += "\u2705"
    if (password.matches("^(?=.*[A-Z]).*"))
        passwordStrength += "\u2705"
    if (password.matches("^(?=.*[0-9]).*"))
        passwordStrength += "\u2705"

    while (passwordStrength.length() < 4) {
        passwordStrength += "\u274E"
    }

    return passwordStrength
}
