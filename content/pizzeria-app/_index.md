+++
title = "Pizzeria App: A Delicious Web App Journey with Go"
outputs = ["Reveal"]
[logo]
src = "/img/shared/stuttgart_gophers.png"
[reveal_hugo]
margin = 0.2
+++

{{< slide background-color="#008080" >}}

<div class="title-slide" style="margin-top: -50px;">
<h1>Pizzeria App</h1>
<h2>A Delicious Web App Journey with Go</h2>
<p>Presented by: AlexTLDR</p>
<div style="margin-top: 60px; display: flex; justify-content: center;">
  <img src="/img/shared/alex-qr-code.png" alt="AlexTLDR Website" style="max-width: 300px; width: 100%;">
</div>
</div>

---

# Why Go?

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">The syntax is minimalistic and clean, inspired by C but much more readable.
      <div class="fragment">
        {{< highlight go >}}
        package main

        import "fmt"

        func main() {
               	message := "Hello, pizza lovers!"
               	fmt.Println(message)
        }
        {{< /highlight >}}
      </div>
    </li>
<li class="fragment">Consistently formatted with gofmt - no style debates.</li>
<li class="fragment">Fast compilation and execution times.</li>
<li class="fragment">Rich Standard Library (our example with the website, HTTP servers, file I/O, JSON handling, etc) - you can build full applications without relying heavily on external dependencies</li>

  </ul>
</div>

---

# Why Go?

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Backwards compatible.</li>
    <li class="fragment">Static typing (preference-based - I love this feature as it makes the code base less confusing).<br><img src="/img/pizzeria-app/static-typing.png" alt="Static Typing" class="fragment" style="max-width: 100%;"></li>

  </ul>
</div>

---

# Why Go?

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Cross-Platform & Deployment-Friendly (run your program as a binary).</li>
    <li class="fragment">DevOps oriented:
      <ul>
        <li class="fragment">Great for containerized apps and microservices</li>
        <li class="fragment">Widely used in cloud-native environments (Docker, Kubernetes, Terraform, ETC.)</li>
        <li class="fragment">Great fit for CLI tools and backend infrastructure</li>
      </ul>
    </li>

  </ul>
</div>

---

# Learning resources

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment"><a href="https://go.dev/tour/" target="_blank">A Tour of Go</a> / <a href="https://tour.ardanlabs.com/" target="_blank">Ultimate Go Tour</a></li>
    <li class="fragment"><a href="https://www.udemy.com/course/learn-go-the-complete-bootcamp-course-golang/?srsltid=AfmBOooEs-gQ7j2eN1Q_c0PqjJdz_qffJYvcui8r4MrSqxYtkBUDnCZr" target="_blank">Go Bootcamp Master Golang with 1000+ Exercises and Projects</a> (last updated in 2021 but still relevant)</li>
    <li class="fragment"><a href="https://boot.dev/" target="_blank">Boot.dev</a></li>
    <li class="fragment"><a href="https://www.ardanlabs.com/" target="_blank">Ardan Labs</a> - in-depth training</li>
  </ul>
</div>

---

# The Pizzeria Project: Overview

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Backend:
      <ul>
        <li class="fragment">Language: Go</li>
        <li class="fragment">Web Server: Native Go HTTP server (from `net/http` package)
            <div class="fragment">
            {{< highlight go >}}
            mux := http.NewServeMux()
            {{< /highlight >}}
            </div>
        </li>
        <li class="fragment">Database: SQLite3 (file-based database)
            <div class="fragment">
            {{< highlight go >}}
            db, err := sql.Open("sqlite3", dbPath)
            if err != nil {
                return err
            }
            defer db.Close()
            {{< /highlight >}}
            </div>
        </li>
      </ul>
    </li>
  </ul>
</div>

---

# Authentication: Google OAuth2

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Google OAuth2 for admin authentication:
      <div class="fragment">
      {{< highlight go >}}
func Initialize() (*OAuthConfig, error) {
	// Load .env file
	if err := godotenv.Load(); err != nil {
		return nil, fmt.Errorf("error loading .env file: %w", err)
	}

	// Get Google OAuth credentials
	clientID := os.Getenv("GOOGLE_CLIENT_ID")
	clientSecret := os.Getenv("GOOGLE_CLIENT_SECRET")
	redirectURL := os.Getenv("GOOGLE_REDIRECT_URL")

	if clientID == "" || clientSecret == "" || redirectURL == "" {
		return nil, errors.New("missing Google OAuth configuration in .env file")
	}

	// Get allowed emails
	allowedEmailsStr := os.Getenv("ALLOWED_EMAILS")
	allowedEmails := strings.Split(allowedEmailsStr, ",")
	for i := range allowedEmails {
		allowedEmails[i] = strings.TrimSpace(allowedEmails[i])
	}

	if len(allowedEmails) == 0 || (len(allowedEmails) == 1 && allowedEmails[0] == "") {
		return nil, errors.New("no allowed email addresses configured in .env file")
	}

	// Create OAuth config
	oauthConfig := &oauth2.Config{
		ClientID:     clientID,
		ClientSecret: clientSecret,
		RedirectURL:  redirectURL,
		Scopes:       []string{"https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"},
		Endpoint:     google.Endpoint,
	}

	return &OAuthConfig{
		GoogleOAuthConfig: oauthConfig,
		AllowedEmails:     allowedEmails,
	}, nil
}
      {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Session Management

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Custom implementation using secure, signed cookies for authentication:
      <div class="fragment">
      {{< highlight go >}}
func SetSecureSessionCookie(w http.ResponseWriter, email string) {
	// Current timestamp for the cookie
	now := time.Now()
	expires := now.Add(SessionDuration)

	// Create the cookie payload: email|expiration_timestamp
	expiresStr := strconv.FormatInt(expires.Unix(), 10)
	payload := fmt.Sprintf("%s|%s", email, expiresStr)

	// Create HMAC signature
	h := hmac.New(sha256.New, cookieSecret)
	h.Write([]byte(payload))
	signature := h.Sum(nil)

	// Encode the payload and signature for the cookie
	encodedPayload := base64.URLEncoding.EncodeToString([]byte(payload))
	encodedSignature := base64.URLEncoding.EncodeToString(signature)

	// Final cookie value: base64(payload).base64(signature)
	cookieValue := fmt.Sprintf("%s.%s", encodedPayload, encodedSignature)

	// Set the cookie
	cookie := http.Cookie{
		Name:     SessionCookieName,
		Value:    cookieValue,
		Path:     "/",
		HttpOnly: true,
		Secure:   true, // Set to true in production
		SameSite: http.SameSiteLaxMode,
		MaxAge:   int(SessionDuration.Seconds()),
		Expires:  expires,
	}

	http.SetCookie(w, &cookie)
	log.Printf("Set secure session cookie for %s, expires: %s", email, expires.Format(time.RFC3339))
}
      {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# File System Operations

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Utilizing standard `os` and `path/filepath` packages for file manipulation:
      <div class="fragment">
      {{< highlight go >}}
var imageURL string
file, header, err := r.FormFile("image_upload")
if err == nil {
	defer file.Close()

	// Create unique filename based on timestamp
	timestamp := time.Now().Unix()
	filename := fmt.Sprintf("%d_%s", timestamp, header.Filename)

	// Ensure filename contains only valid characters
	filename = strings.ReplaceAll(filename, " ", "-")

	// Save the file
	filePath := filepath.Join("static", "images", "menu", filename)
	dst, err := os.Create(filePath)
	if err != nil {
		m.adminError(w, r, err, http.StatusInternalServerError, "CreateMenuItem - saving image")
		return
	}
	defer dst.Close()

	// Copy the file content
	_, err = dst.ReadFrom(file)
	if err != nil {
		m.adminError(w, r, err, http.StatusInternalServerError, "CreateMenuItem - saving image")
		return
	}

	// Set the image URL
	imageURL = "/" + filePath // Add leading slash for web URLs
}
      {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# File System Operations

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Utilizing standard `os` and `path/filepath` packages for file manipulation and guarding against traversal attacks:
      <div class="fragment">
      {{< highlight go >}}
  var imageURL string

	file, header, err := r.FormFile("image_upload")
	if err == nil {
		defer file.Close()

		// Validate that the file is an image
		if !m.isValidImageExtension(header.Filename) {
			m.clientError(w, http.StatusBadRequest, "Invalid file type. Only image files (jpg, jpeg, png, gif, webp, bmp, svg) are allowed.")
			return
		}

		// Create a completely random filename with timestamp prefix
		timestamp := time.Now().Unix()
		extension := filepath.Ext(header.Filename) // Get the file extension
		randomName := fmt.Sprintf("%d_%s%s", timestamp, uuid.New().String(), extension)

		// Save the file
		filePath := filepath.Join("static", "images", "menu", randomName)

		dst, err := os.Create(filePath)
		if err != nil {
			m.adminError(w, r, err, http.StatusInternalServerError, "CreateMenuItem - saving image")
			return
		}

		defer dst.Close()

		// Copy the file content
		_, err = dst.ReadFrom(file)
		if err != nil {
			m.adminError(w, r, err, http.StatusInternalServerError, "CreateMenuItem - saving image")
			return
		}

		// Set the image URL
		imageURL = "/" + filePath // Add leading slash for web URLs
	}
      {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Frontend

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">Templating: Go's built-in `html/template` package
            <div class="fragment">
            {{< highlight go >}}
templates := map[string]*template.Template{
		"index.html":           template.Must(template.New("index.html").Funcs(funcMap).ParseFiles("templates/index.html", "templates/header.html", "templates/footer.html", "templates/category-nav.html")),
		"login.html":           template.Must(template.New("login.html").Funcs(funcMap).ParseFiles("templates/login.html")),
		"admin-dashboard.html": template.Must(template.New("admin-dashboard.html").Funcs(funcMap).ParseFiles("templates/admin-dashboard.html")),
		"menu-form.html":       template.Must(template.New("menu-form.html").Funcs(funcMap).ParseFiles("templates/menu-form.html")),
}
            {{< /highlight >}}
            </div>
        </li>
        <li class="fragment">CSS Framework: Tailwind CSS V4</li>
        <li class="fragment">JavaScript: Minimal usage via inline scripts in templates for eye candy stuff like highlighting the menu category or jumping directly to a menu category (not relevant for this topic)
        </li>
      </ul>
  </ul>
</div>

---

# Development Tools

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">Migrations: Goose for database migrations
            <div class="fragment">
            {{< highlight go >}}
            -- +goose Up
            -- SQL in this section is executed when the migration is applied.
            CREATE TABLE menu_items (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                name TEXT NOT NULL,
                description TEXT,
                price REAL NOT NULL,
                small_price REAL,
                category TEXT NOT NULL,
                image_url TEXT, -- Stores the relative path like /static/images/menu/image.jpg
                created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
            );

            CREATE TABLE users (
                id INTEGER PRIMARY KEY AUTOINCREMENT,
                username TEXT NOT NULL UNIQUE,
                password_hash TEXT NOT NULL,
                created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
                updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
            );

            -- +goose Down
            -- SQL in this section is executed when the migration is rolled back.
            DROP TABLE users;
            DROP TABLE menu_items;
            {{< /highlight >}}
            </div>
        </li>
        <li class="fragment">Hot Reload: Air for Go hot reloading during development</li>
        <li class="fragment">Containerization: Docker support (via Dockerfile and docker-compose.yml)</li>
        <li class="fragment">Environment Management: godotenv for .env file loading</li>
      </ul>
  </ul>
</div>

---

# Architecture

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Project Structure:
      <ul>
        <li class="fragment">cmd/server: Main application entry point</li>
        <li class="fragment">db: Database initialization and migrations</li>
        <li class="fragment">internal: Core application logic separated into modules
            <div class="fragment">
            {{< highlight go >}}
            - auth: Google OAuth implementation
            - handlers: HTTP request handlers
            - middleware: Authentication and session middleware
            - models: Data models and database interactions
            {{< /highlight >}}
            </div>
        </li>
        <li class="fragment">static: Static assets (CSS, JavaScript, images)</li>
        <li class="fragment">templates: HTML templates for rendering pages</li>
      </ul>
    </li>
  </ul>
</div>

---

# Database Schema

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">menu_items: Restaurant menu items with pricing and categories</li>
        <li class="fragment">flash_messages: Announcements/notifications to display on the website</li>
        <li class="fragment">users: (Deprecated) Previously used for authentication, now replaced with Google OAuth</li>
      </ul>
  </ul>
</div>

---

# Authentication Flow

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">User navigates to `/login`</li>
        <li class="fragment">User is redirected to Google OAuth login</li>
        <li class="fragment">Google redirects back with an authorization code</li>
        <li class="fragment">App exchanges code for a token and retrieves user info</li>
        <li class="fragment">App validates if the user's email is in the allowed list</li>
        <li class="fragment">Upon successful validation, a secure signed session cookie is created</li>
        <li class="fragment">Admin routes are protected by middleware that verifies this cookie</li>
      </ul>
  </ul>
</div>

---

# Key Features

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Menu Management:
      <ul>
        <li class="fragment">CRUD operations for menu items</li>
        <li class="fragment">Category-based organization</li>
        <li class="fragment">Support for different pricing (regular/small sizes)</li>
        <li class="fragment">Image upload support for menu items</li>
      </ul>
    </li>
  </ul>
</div>

---

# Admin Dashboard:

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">Secure admin area accessible only to authorized emails</li>
        <li class="fragment">Menu item management</li>
        <li class="fragment">Flash message management</li>
      </ul>
  </ul>
</div>

---

# Public Website:

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">Display of menu items organized by categories</li>
        <li class="fragment">Display of flash messages/announcements</li>
        <li class="fragment">Responsive design using Tailwind CSS</li>
      </ul>
  </ul>
</div>

---

# Security:

<div class="responsive-container">
  <ul class="responsive-list">
      <ul>
        <li class="fragment">Google OAuth for authentication</li>
        <li class="fragment">HMAC-SHA256 signed cookies for session management</li>
        <li class="fragment">Whitelist of allowed admin emails</li>
      </ul>
  </ul>
</div>

---

# Standard Library Packages Used

<div class="responsive-container">
    <div class="fragment">
    {{< highlight go >}}
    - database/sql: Core database operations
    - net/http: HTTP server and client operations
    - html/template: HTML templating
    - time: Time handling and formatting
    - context: Request context management
    - encoding/json: JSON encoding/decoding
    - encoding/base64: Base64 encoding/decoding for cookies
    - crypto/hmac: HMAC signatures for secure cookies
    - crypto/sha256: SHA256 hashing for HMAC
    - crypto/rand: Secure random number generation
    - path/filepath: File path operations
    - strings: String manipulation
    - log: Logging
    - os: File manipulation and operations
    {{< /highlight >}}
    </div>
</div>


---

# External Dependencies

<div class="responsive-container">
    <div class="fragment">
    {{< highlight go >}}
    - github.com/joho/godotenv: For loading environment variables from .env files
    - github.com/mattn/go-sqlite3: SQLite3 driver for Go
    - github.com/pressly/goose/v3: Database migration tool
    - golang.org/x/oauth2: Google OAuth2 integration
    - tailwindcss: Frontend CSS framework (not Go related)
    {{< /highlight >}}
    </div>
</div>

---

# Deployment

<div class="responsive-container">
    <div class="fragment">
    {{< highlight go >}}
    - Docker support via Dockerfile and docker-compose.yml
    - Environment configuration via .env files
    - Database migrations for setting up the initial schema and seed data
    - Using for hosting a GCP VM via Compute Engine
    - Caddy for HTTPS cert and routing
    {{< /highlight >}}
    </div>
</div>

---

# Q&A

<div style="display: flex; justify-content: center; align-items: center; width: 100%; gap: 80px; margin-top: 40px;">
    <div style="text-align: center;">
        <p style="font-size: 24px; font-weight: bold; margin-bottom: 20px;">Presentation Slides</p>
        <img src="/img/pizzeria-app/slides-qr-code2.png" alt="Slides QR Code" style="max-width: 500px; width: 100%;">
    </div>
    <div style="text-align: center;">
        <p style="font-size: 24px; font-weight: bold; margin-bottom: 20px;">Pizzeria Website</p>
        <img src="/img/pizzeria-app/pizzeria-qr-code2.png" alt="Pizzeria Website QR Code" style="max-width: 500px; width: 100%;">
    </div>
</div>
