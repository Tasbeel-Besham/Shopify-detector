
const puppeteer = require("puppeteer");

module.exports = async (req, res) => {
  const { query } = req;
  const domain = query.domain;

  if (!domain) {
    return res.status(400).json({ error: "Missing domain parameter" });
  }

  try {
    const browser = await puppeteer.launch({
      headless: "new",
      args: ["--no-sandbox", "--disable-setuid-sandbox"]
    });
    const page = await browser.newPage();
    await page.goto("https://" + domain, { waitUntil: "domcontentloaded", timeout: 30000 });

    const themeName = await page.evaluate(() => {
      try {
        return window.Shopify && Shopify.theme && Shopify.theme.name
          ? Shopify.theme.name
          : null;
      } catch (e) {
        return null;
      }
    });

    await browser.close();

    if (!themeName) {
      return res.status(200).json({ theme: "Unknown", domain });
    }

    return res.status(200).json({ theme: themeName, domain });
  } catch (error) {
    return res.status(500).json({ error: "Failed to detect theme", details: error.toString() });
  }
};
