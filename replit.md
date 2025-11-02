# TESLA Investment Platform - SoleasPay Integration

## Overview

TESLA Investment Platform is an investment and payment integration system built around SoleasPay, a mobile money payment gateway serving African markets. The platform, branded with Tesla (Elon Musk's company) theme, enables users to deposit funds via mobile money operators (MTN Money, Orange Money, Moov Money, Wave, Airtel Money, etc.) across multiple African countries and automatically credit their accounts upon payment confirmation.

## Recent Changes (November 2025)

**Complete Tesla Rebranding:**
- All NVIDIA references replaced with TESLA throughout the platform
- Color scheme changed from NVIDIA green (#76b900) to Tesla red (#E82127)
- Design updated to match Tesla's bold, modern aesthetic
- Certificate content updated to reflect electric vehicle investment theme
- Contact information updated for Tesla support channels

The system implements a three-tier validation approach:
1. Immediate verification after 3 seconds
2. Webhook-based automatic validation
3. Manual verification fallback

## User Preferences

Preferred communication style: Simple, everyday language.

## Brand Identity

**Design Theme:**
- **Primary Color**: Tesla Red (#E82127) - replaces previous green theme
- **Brand Name**: TESLA (inspired by Tesla Inc.)
- **Visual Style**: Bold, modern, electric vehicle innovation aesthetic
- **Icons**: Electric/automotive themed (⚡ bolt, 🚗 car, 🔋 battery, 📊 charts)

**Contact Information:**
- **Telegram Support**: @sarahicardi12
- **Telegram Channel**: t.me/teslaprojectmusk
- **WhatsApp Group**: https://chat.whatsapp.com/FXxiB87KkPG0mFxNYEG7Hc?mode=wwt
- **Phone**: +237 674672211

## System Architecture

### Backend Architecture

**Technology Stack:**
- **Language**: PHP (5.5+)
- **Email Library**: PHPMailer 7.0
- **Dependency Management**: Composer

**Payment Processing Flow:**

The system handles SoleasPay payment integration through a multi-stage validation process:

1. **User Initiates Deposit**: User enters desired amount
2. **Payment Request**: System creates payment request via SoleasPay API with exact amount
3. **Three-Tier Validation**:
   - Immediate check after 3 seconds via SoleasPay API
   - Webhook callback for asynchronous confirmation
   - Manual verification as fallback option

**Rationale**: This multi-tier approach ensures maximum reliability. Immediate verification provides instant gratification when possible, webhooks handle delayed confirmations automatically, and manual verification prevents any payments from being lost due to technical issues.

**Key Design Decisions:**

1. **Exact Amount Crediting**: The platform credits the exact amount deposited by the user to their account without fees or deductions.

2. **Webhook Callback System**: 
   - Endpoint: `soleaspay_callback.php`
   - Receives JSON notifications from SoleasPay
   - Logs all callbacks to `soleaspay_callback.log`
   - **Security Note**: SHA-512 private key verification (via `x-private-key` header) is documented but not yet implemented

3. **Reference Generation**: Uses external reference format `DEP_{user_id}_{timestamp}` for tracking deposits

**Alternatives Considered**: 
- Single verification method was rejected for reliability concerns
- Immediate crediting without verification was rejected for security
- Fee deduction was rejected per business requirements

**Pros**: High reliability, good user experience, automatic processing
**Cons**: More complex implementation, requires webhook configuration by merchant

### Authentication & API Integration

**SoleasPay API Authentication:**
- **Method**: Bearer Token and x-api-key depending on endpoint prefix
- **API Key**: `4O6UHCMtqewMkld4MSaZUWYr_M9-JWrlYT0oB3AZ9To-AP`
- **Rate Limit**: 500 requests/hour per IP
- **Endpoints**:
  - `/api/action/*` - Bearer Token authentication
  - `/api/agent/*` - x-api-key authentication

**Webhook Security:**
- Callback endpoint receives `x-private-key` header
- Value should be SHA-512 hash of merchant secret
- **Current Status**: Verification not yet implemented in webhook handler

### Supported Geographic Markets

The platform supports mobile money payments across 13 African countries:

- 🇧🇯 Benin: MTN Money, Moov
- 🇧🇫 Burkina Faso: Moov Money, Orange Money
- 🇨🇲 Cameroon: MTN Mobile Money, Orange Money
- 🇨🇬 Congo-Brazzaville: MTN Money, Airtel Money
- 🇨🇩 DRC: Orange Money, Vodacom, Airtel Money
- 🇨🇮 Côte d'Ivoire: MTN Money, Wave, Moov Money, Orange Money
- 🇲🇱 Mali: Orange Money, Moov
- 🇬🇦 Gabon: Airtel Money
- 🇹🇬 Togo: Moov Money, T-Money
- 🇰🇪 Kenya: M-Pesa
- 🇷🇼 Rwanda: MTN Mobile Money
- 🇸🇳 Senegal: Free Money, Wave, Expresso, Wizall, Orange Money
- 🇺🇬 Uganda: Mobile Money, Airtel

## External Dependencies

### Third-Party Services

**SoleasPay Payment Gateway:**
- **Purpose**: Mobile money payment collection
- **Base URL**: https://soleaspay.com
- **Integration Type**: REST API with webhook callbacks
- **Required Configuration**: 
  - Webhook URL must be configured in SoleasPay dashboard
  - Callback endpoint URL format: `https://{domain}.repl.co/soleaspay_callback.php`
- **Callback Payload Structure**:
  ```json
  {
    "success": boolean,
    "status": "SUCCESS" | "RECEIVED" | "PROCESSING" | "REFUND",
    "data": {
      "reference": "internal_reference",
      "external_reference": "merchant_reference",
      "amount": integer,
      "currency": "XOF"
    }
  }
  ```

### PHP Libraries

**PHPMailer (v7.0):**
- **Purpose**: Email notification system
- **Installation**: Via Composer
- **Use Case**: Likely used for transaction confirmations and user notifications
- **Features**: SMTP support, UTF-8 handling, attachment support

### Deployment Platform

**Replit Hosting:**
- Platform is designed for Replit deployment
- Dynamic domain configuration required for webhook setup
- Static file serving capability (index2.html suggests default landing page)

### Data Storage

**Database**: Not explicitly defined in current codebase
- Payment transaction records need persistence
- User account balance storage required
- Deposit history tracking needed
- **Note**: Database implementation (likely MySQL/PostgreSQL) to be added