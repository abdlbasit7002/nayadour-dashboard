// Save this file as: netlify/functions/send-code.js  (inside a folder named exactly "netlify/functions")
//
// This function sends the 6-digit login code by email using Resend.
// The Resend API key stays secret on the server side (as a Netlify environment
// variable) — it never appears in the browser.

exports.handler = async (event) => {
  if (event.httpMethod !== 'POST') {
    return { statusCode: 405, body: 'Method Not Allowed' };
  }
  try {
    const { email, code, appName } = JSON.parse(event.body);
    if (!email || !code) {
      return { statusCode: 400, body: JSON.stringify({ error: 'email and code are required' }) };
    }

    const brand = appName || 'NAYA DOUR';

    const res = await fetch('https://api.resend.com/emails', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${process.env.RESEND_API_KEY}`
      },
      body: JSON.stringify({
        // Resend's shared test sender — works immediately with no domain setup.
        // Once you verify your own domain in Resend, you can change this to
        // something like "NAYA DOUR <login@yourdomain.com>".
        from: 'Order Intelligence <onboarding@resend.dev>',
        to: [email],
        subject: `${brand} — Your login code is ${code}`,
        html: `
          <div style="font-family: Arial, sans-serif; max-width: 420px; margin: 0 auto; padding: 30px; background:#0B0E12; color:#F2EFE9; border-radius:14px;">
            <h2 style="margin:0 0 4px;">${brand}</h2>
            <p style="color:#A8AEB8; margin:0 0 24px;">Order Intelligence Dashboard</p>
            <p style="margin:0 0 8px;">Your one-time login code is:</p>
            <div style="font-size:32px; font-weight:700; letter-spacing:8px; color:#C9944A; margin-bottom:20px;">${code}</div>
            <p style="color:#A8AEB8; font-size:13px;">This code expires in 10 minutes. If you didn't request this, you can safely ignore this email.</p>
          </div>
        `
      })
    });

    if (!res.ok) {
      const errText = await res.text();
      console.error('Resend error:', errText);
      return { statusCode: 500, body: JSON.stringify({ error: 'Failed to send email', details: errText }) };
    }

    return {
      statusCode: 200,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ success: true })
    };
  } catch (err) {
    return { statusCode: 500, body: JSON.stringify({ error: err.message }) };
  }
};
