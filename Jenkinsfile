//Oleg Milyukov
//import java.io.File 

node {
    stage('Generate Password') {
        
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
            passwordHistory = passwordHistoryFile.readLines()
            println("Password History contains: ${passwordHistory}")
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
//                    node(label) {
                            def currentPassword = generatePassword(params.password_length.toInteger(), params.attempts_number.toInteger())
//                            println("Generated password: ${currentPassword}")
                            def currentStrength = isPasswordStrong(currentPassword)
//                            println("Generated password: ${currentStrength}")

                            if (!passwordHistory.contains(currentPassword)) {
                                
                                resultPasswords.add(currentPassword)
                                passwordsStrengths.add(currentStrength)

                                passwordHistory.add(currentPassword)
                                println("Added")
                                println("Password History contains: ${passwordHistory}")
                                passwordHistoryFile.write((String)(currentPassword + "\n"), true)
                                println("Written 1")
                                passwordHistoryFile.write("\n", true)
                                println("Written 2")
                            }
                            
//                        }
//                    catch (Exception e) {
//                        println(e.getMessage())
//                    }
            }
        }

        parallel(builders)
        for (int i = 0; i < password_amount.toInteger(); i++) {
            println("Generated password: ${resultPasswords[i]}")
            println("Password strength: ${passwordsStrengths[i]}")
        }

        println("Generated passwords: ${resultPasswords}")
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

        println("I'm after FOR")

        if (password.toString().matches("^(?=.*[a-z])(?=.*[A-Z])(?=.*[0-9]).*")) {
            println("I'm in IF")
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
        passwordStrength += "\u2611"
    }

    return passwordStrength
}
