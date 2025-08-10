<!DOCTYPE html>
<html lang="az">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Daxil ol | Motivasiya Portalı</title>
    <style>
        :root {
            --primary-color: #4361ee;
            --secondary-color: #3f37c9;
            --accent-color: #4cc9f0;
            --success-color: #38b000;
            --error-color: #ef233c;
            --text-color: #2b2d42;
            --light-bg: #f8f9fa;
        }
        
        body {
            font-family: 'Segoe UI', sans-serif;
            background-color: var(--light-bg);
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            color: var(--text-color);
        }
        
        .login-container {
            background: white;
            padding: 30px;
            border-radius: 15px;
            box-shadow: 0 8px 16px rgba(0,0,0,0.1);
            width: 380px;
            transition: all 0.3s ease;
        }
        
        .login-header {
            text-align: center;
            margin-bottom: 30px;
        }
        
        .login-header h1 {
            color: var(--primary-color);
            font-size: 28px;
            margin-bottom: 10px;
        }
        
        .input-group {
            margin-bottom: 20px;
        }
        
        input {
            width: 100%;
            padding: 14px;
            border: 2px solid #e9ecef;
            border-radius: 8px;
            box-sizing: border-box;
            font-size: 15px;
        }
        
        .login-btn {
            width: 100%;
            padding: 14px;
            background-color: var(--primary-color);
            color: white;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            font-size: 16px;
            transition: all 0.3s;
        }
        
        .error-message {
            color: var(--error-color);
            font-size: 14px;
            margin-top: 5px;
            display: none;
        }
        
        .social-icon {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 24px;
            cursor: pointer;
        }
    </style>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
</head>
<body>
    <div class="login-container">
        <div class="login-header">
            <h1>Daxil ol</h1>
            <p>Uğura gedən yol buradan başlayır!</p>
        </div>
        
        <form id="loginForm">
            <div class="input-group">
                <label>Email</label>
                <input type="email" id="email" placeholder="Email ünvanınızı daxil edin" required>
                <div class="error-message" id="emailError"></div>
            </div>
            
            <div class="input-group">
                <label>Şifrə</label>
                <input type="password" id="password" placeholder="Şifrənizi daxil edin" required>
                <div class="error-message" id="passwordError"></div>
            </div>
            
            <button type="submit" class="login-btn">DAXİL OL</button>
        </form>
        
        <div style="text-align: center; margin: 20px 0;">
            <a href="sifreni-unutdum.html" style="color: var(--primary-color);">Şifrəni unutdum?</a>
        </div>
        
        <div style="text-align: center; margin-bottom: 20px;">
            <p>Və ya sosial şəbəkələrlə daxil olun</p>
            <div style="display: flex; justify-content: center; gap: 15px;">
                <div class="social-icon" style="background-color: #3b5998;" id="facebookLogin">
                    <i class="fab fa-facebook-f"></i>
                </div>
                <div class="social-icon" style="background-color: #1DA1F2;" id="twitterLogin">
                    <i class="fab fa-twitter"></i>
                </div>
                <div class="social-icon" style="background-color: #DB4437;" id="googleLogin">
                    <i class="fab fa-google"></i>
                </div>
            </div>
        </div>
        
        <div style="text-align: center; margin-top: 20px;">
            <p>Hesabınız yoxdur? <a href="qeydiyyat.html" style="color: var(--primary-color);">Qeydiyyatdan keçin</a></p>
        </div>
    </div>

    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.0.2/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.0.2/firebase-auth-compat.js"></script>
    
    <script>
        // Firebase konfiqurasiyası
        const firebaseConfig = {
            apiKey: "YOUR_API_KEY",
            authDomain: "YOUR_AUTH_DOMAIN",
            projectId: "YOUR_PROJECT_ID",
            storageBucket: "YOUR_STORAGE_BUCKET",
            messagingSenderId: "YOUR_SENDER_ID",
            appId: "YOUR_APP_ID"
        };

        // Firebase-i işə sal
        firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();

        // Email/Şifrə ilə giriş
        document.getElementById('loginForm').addEventListener('submit', (e) => {
            e.preventDefault();
            
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            
            // Validasiya
            if (!validateEmail(email)) {
                showError('emailError', 'Zəhmət olmasa düzgün email daxil edin');
                return;
            }
            
            if (password.length < 6) {
                showError('passwordError', 'Şifrə ən azı 6 simvol olmalıdır');
                return;
            }
            
            // Firebase ilə giriş
            auth.signInWithEmailAndPassword(email, password)
                .then((userCredential) => {
                    // Giriş uğurlu
                    window.location.href = 'dashboard.html';
                })
                .catch((error) => {
                    // Xəta baş verdi
                    const errorCode = error.code;
                    let errorMessage = error.message;
                    
                    if (errorCode === 'auth/user-not-found') {
                        errorMessage = 'Bu email ilə qeydiyyatlı istifadəçi tapılmadı';
                    } else if (errorCode === 'auth/wrong-password') {
                        errorMessage = 'Yanlış şifrə';
                    }
                    
                    showError('passwordError', errorMessage);
                });
        });

        // Facebook ilə giriş
        document.getElementById('facebookLogin').addEventListener('click', () => {
            const provider = new firebase.auth.FacebookAuthProvider();
            socialLogin(provider);
        });

        // Twitter ilə giriş
        document.getElementById('twitterLogin').addEventListener('click', () => {
            const provider = new firebase.auth.TwitterAuthProvider();
            socialLogin(provider);
        });

        // Google ilə giriş
        document.getElementById('googleLogin').addEventListener('click', () => {
            const provider = new firebase.auth.GoogleAuthProvider();
            socialLogin(provider);
        });

        // Sosial giriş üçün ümumi funksiya
        function socialLogin(provider) {
            auth.signInWithPopup(provider)
                .then((result) => {
                    // Uğurlu giriş
                    window.location.href = 'dashboard.html';
                }).catch((error) => {
                    // Xəta baş verdi
                    console.error(error);
                    alert('Giriş zamanı xəta baş verdi: ' + error.message);
                });
        }

        // Email validasiya funksiyası
        function validateEmail(email) {
            const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
            return re.test(email);
        }

        // Xəta mesajını göstərmək
        function showError(elementId, message) {
            const element = document.getElementById(elementId);
            element.textContent = message;
            element.style.display = 'block';
        }
    </script>
</body>
</html>
