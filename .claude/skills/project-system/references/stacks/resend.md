# Resend Reference

## Setup

```bash
npm install resend
```

```ts
// lib/resend.ts
import { Resend } from "resend";

export const resend = new Resend(process.env.RESEND_API_KEY);
```

Set `RESEND_API_KEY` in your environment variables. Get the key from [resend.com/api-keys](https://resend.com/api-keys).

## Sending Emails

```ts
import { resend } from "@/lib/resend";

// Basic email
const { data, error } = await resend.emails.send({
  from: "App <notifications@yourdomain.com>",
  to: ["user@example.com"],
  subject: "Welcome to the app",
  html: "<p>Thanks for signing up.</p>",
});

// With React Email template
import { WelcomeEmail } from "@/emails/welcome";

const { data, error } = await resend.emails.send({
  from: "App <notifications@yourdomain.com>",
  to: [user.email],
  subject: "Welcome to the app",
  react: WelcomeEmail({ name: user.name }),
});

// With CC, BCC, reply-to
const { data, error } = await resend.emails.send({
  from: "Support <support@yourdomain.com>",
  to: [recipient],
  cc: ["manager@example.com"],
  bcc: ["logs@example.com"],
  replyTo: "support@yourdomain.com",
  subject: "Your request has been received",
  react: SupportEmail({ ticketId }),
});
```

## React Email Templates

```bash
npm install @react-email/components
```

```tsx
// emails/welcome.tsx
import {
  Body,
  Container,
  Head,
  Heading,
  Html,
  Link,
  Preview,
  Section,
  Text,
} from "@react-email/components";

interface WelcomeEmailProps {
  name: string;
  loginUrl?: string;
}

export function WelcomeEmail({ name, loginUrl = "https://app.example.com/login" }: WelcomeEmailProps) {
  return (
    <Html>
      <Head />
      <Preview>Welcome to the app, {name}</Preview>
      <Body style={body}>
        <Container style={container}>
          <Heading style={heading}>Welcome, {name}</Heading>
          <Text style={text}>
            Your account is ready. Sign in to get started.
          </Text>
          <Section style={buttonSection}>
            <Link href={loginUrl} style={button}>
              Sign In
            </Link>
          </Section>
          <Text style={footer}>
            If you did not create this account, ignore this email.
          </Text>
        </Container>
      </Body>
    </Html>
  );
}

// Inline styles (email clients do not support CSS classes reliably)
const body = { backgroundColor: "#f6f9fc", fontFamily: "-apple-system, sans-serif" };
const container = { backgroundColor: "#ffffff", margin: "0 auto", padding: "40px", maxWidth: "480px" };
const heading = { fontSize: "24px", fontWeight: "600", color: "#1a1a1a", margin: "0 0 16px" };
const text = { fontSize: "16px", lineHeight: "1.5", color: "#4a4a4a", margin: "0 0 24px" };
const buttonSection = { textAlign: "center" as const, margin: "32px 0" };
const button = { backgroundColor: "#171717", borderRadius: "6px", color: "#fff", display: "inline-block", fontSize: "14px", fontWeight: "600", padding: "12px 24px", textDecoration: "none" };
const footer = { fontSize: "13px", color: "#8a8a8a", margin: "32px 0 0" };
```

**Preview templates locally:**

```bash
npx email dev
```

This starts a local server at `localhost:3000` to preview all templates in the `emails/` directory.

## Using in Server Actions / Route Handlers

```ts
// app/actions/invite.ts
"use server";
import { resend } from "@/lib/resend";
import { InviteEmail } from "@/emails/invite";

export async function sendInvite(email: string, projectName: string) {
  const { data, error } = await resend.emails.send({
    from: "App <invites@yourdomain.com>",
    to: [email],
    subject: `You're invited to ${projectName}`,
    react: InviteEmail({ projectName }),
  });

  if (error) {
    return { error: `Failed to send invite: ${error.message}` };
  }

  return { emailId: data?.id };
}
```

## Batch Sending

Send up to 100 emails in a single API call.

```ts
const { data, error } = await resend.batch.send([
  {
    from: "App <notifications@yourdomain.com>",
    to: ["user1@example.com"],
    subject: "Weekly digest",
    react: DigestEmail({ userId: "user1" }),
  },
  {
    from: "App <notifications@yourdomain.com>",
    to: ["user2@example.com"],
    subject: "Weekly digest",
    react: DigestEmail({ userId: "user2" }),
  },
]);
```

## Webhooks

Resend sends webhook events for email delivery status. Configure the webhook URL in the Resend dashboard.

```ts
// app/api/webhooks/resend/route.ts
import { NextRequest, NextResponse } from "next/server";
import { Webhook } from "svix";

const webhookSecret = process.env.RESEND_WEBHOOK_SECRET!;

export async function POST(request: NextRequest) {
  const body = await request.text();
  const headers = {
    "svix-id": request.headers.get("svix-id")!,
    "svix-timestamp": request.headers.get("svix-timestamp")!,
    "svix-signature": request.headers.get("svix-signature")!,
  };

  const webhook = new Webhook(webhookSecret);

  let event: any;
  try {
    event = webhook.verify(body, headers);
  } catch {
    return NextResponse.json({ error: "Invalid signature" }, { status: 400 });
  }

  switch (event.type) {
    case "email.sent":
      console.log("Email sent:", event.data.email_id);
      break;
    case "email.delivered":
      await markEmailDelivered(event.data.email_id);
      break;
    case "email.bounced":
      await handleBounce(event.data.to, event.data.bounce_type);
      break;
    case "email.complained":
      await handleComplaint(event.data.to);
      break;
  }

  return NextResponse.json({ received: true });
}
```

Install the Svix package for webhook verification: `npm install svix`.

## Domain Verification

1. Add your domain in the Resend dashboard (Domains > Add Domain).
2. Add the DNS records Resend provides:
   - **MX record** for receiving (if needed)
   - **TXT record** for SPF verification
   - **CNAME records** for DKIM signing
3. Wait for verification (usually under 10 minutes).
4. Send from `anything@yourdomain.com`.

Without a verified domain, you can only send from `onboarding@resend.dev` to your own email.

## Error Handling

```ts
const { data, error } = await resend.emails.send({ /* ... */ });

if (error) {
  // error.name: "validation_error" | "rate_limit_exceeded" | "not_found" | etc.
  // error.message: Human-readable description
  console.error(`Email failed: [${error.name}] ${error.message}`);

  // Retry logic for transient errors
  if (error.name === "rate_limit_exceeded") {
    // Wait and retry (Resend rate limit: 10 requests/second on free plan)
    await new Promise((r) => setTimeout(r, 1000));
    return sendEmailWithRetry(params);
  }

  return { error: error.message };
}

// data.id is the email ID for tracking
console.log("Sent:", data.id);
```

## Testing in Development

**Option 1:** Use the Resend test API key. Emails are simulated (not actually delivered) but appear in the Resend dashboard logs.

**Option 2:** Send to your own email from `onboarding@resend.dev` (no domain verification required).

**Option 3:** Preview templates locally with `npx email dev` without sending.

```ts
// Conditionally skip sending in development
export async function sendEmail(params: EmailParams) {
  if (process.env.NODE_ENV === "development" && !process.env.SEND_REAL_EMAILS) {
    console.log("[Email Preview]", params.subject, "->", params.to);
    return { emailId: "dev-preview" };
  }

  const { data, error } = await resend.emails.send(params);
  if (error) throw new Error(error.message);
  return { emailId: data?.id };
}
```
