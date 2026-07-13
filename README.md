# InvoiceFlow Monorepo

## Setup Instructions

### Prerequisites
- Node.js >= 18.x
- PHP >= 8.4
- Composer
- Docker & Docker Compose

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```

2. **Frontend Setup**
   ```bash
   cd frontend
   npm install
   npm start
   ```
   Access the frontend at `http://localhost:3000`

3. **Backend Setup**
   ```bash
   cd backend
   composer install
   php artisan migrate
   php artisan serve
   ```
   Access the backend at `http://localhost:8000`

4. **Start Services with Docker Compose**
   ```bash
   docker-compose up
   ```

### Services
- **PostgreSQL**: Available at `localhost:5432`
- **Redis**: Available at `localhost:6379`

### Environment Variables
- Copy `.env.example` to `.env` in the `backend` directory and configure as needed.

### Storage
- Ensure S3-compatible storage configuration is set in the `.env` file.

### Tailwind CSS
- Tailwind CSS is configured and ready to use in the frontend.

### Troubleshooting
- Ensure Docker is running if services are not accessible.
- Check logs for errors using `docker-compose logs`.
