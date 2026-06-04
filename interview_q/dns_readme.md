Think of **DNS (Domain Name System)** as the **phonebook of the internet**.

While we humans remember websites by names like `google.com` or `wikipedia.org`, computers communicate using numbers called **IP addresses** (like `192.0.2.1`). DNS is the service that translates those human-friendly domain names into machine-readable IP addresses so your browser can load the correct website.

---

### How the DNS Process Works (The "Lookup")

When you type a URL into your browser, a "DNS lookup" happens behind the scenes in milliseconds. Here is the step-by-step journey:

1.  **The Request:** You type `example.com`. Your computer first checks its own local **cache** to see if it remembers the IP. If not, it asks a **Recursive Resolver** (usually provided by your ISP).
2.  **The Root Server:** The Resolver asks a **Root Nameserver**. The Root server doesn't know the IP, but it knows where the **TLD (Top-Level Domain)** servers are (like `.com` or `.net`).
3.  **The TLD Server:** The Resolver then asks the `.com` TLD server. This server points the Resolver toward the **Authoritative Nameserver** for that specific domain.
4.  **The Authoritative Server:** This is the final stop. This server holds the actual IP address for `example.com`. It hands the IP back to the Resolver.
5.  **The Connection:** The Resolver gives the IP address to your browser, which then connects to the web server to display the site.

---

### Key Components to Know

If you're explaining this in a technical context (like an interview), keep these terms in your back pocket:

- **IP Address:** The unique numerical label for every device on a network.
- **DNS Records:** These are the instructions stored on the server. The most common is the **A Record**, which maps a domain to an IPv4 address.
- **TTL (Time to Live):** This tells the recursive resolver how long to "cache" or remember the DNS information before asking for an update.
- **Propagation:** When you change your DNS settings, it takes time for those changes to update across all servers worldwide (usually up to 24–48 hours).

### Why is DNS Important?

Without DNS, the internet would be nearly impossible for people to navigate. You would have to memorize a long string of numbers for every single website you wanted to visit. It also allows website owners to change their web hosting (and their IP address) without the user ever noticing—because the domain name stays the same!

Does that help clarify how your browser finds where to go?
