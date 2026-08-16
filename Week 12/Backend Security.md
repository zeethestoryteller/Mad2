# Lecture Notes: Backend Security (Supply Chain, DoS & Password Practices)

## 1. Introduction to Backend Security

Securing the backend requires understanding multiple layers of the software stack—including application code, dependencies, compilers, operating systems, and deployment environments [[00:33](https://www.youtube.com/watch?v=FryxNwEeZDs&t=33)]. Security is not just a feature; it is an architectural mindset that spans development and system administration [[27:08](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1628)].

---

## 2. Package Management & Supply Chain Vulnerabilities

### **The Importance of Dependency Management**

* Most web applications (like Flask apps) rely heavily on third-party libraries [[01:25](https://www.youtube.com/watch?v=FryxNwEeZDs&t=85)].
* **Version Pinned Requirements:** Always specify exact version numbers in your `requirements.txt` file (e.g., `graphql-core==3.2.3` rather than just `graphql-core`) [[02:48](https://www.youtube.com/watch?v=FryxNwEeZDs&t=168)]. Unpinned packages can automatically upgrade over time, introducing breaking changes or unvetted updates.

### **Supply Chain Risks**

* **The Log4j Vulnerability (2021):** A critical remote code execution bug found in the popular Java logging library `log4j` [[06:48](https://www.youtube.com/watch?v=FryxNwEeZDs&t=408)]. Malicious inputs (such as crafted strings in HTTP user-agent headers) forced vulnerable servers to query external servers and execute arbitrary code payloads [[07:31](https://www.youtube.com/watch?v=FryxNwEeZDs&t=451)]. The bug went unnoticed in widely used production systems for eight years [[08:30](https://www.youtube.com/watch?v=FryxNwEeZDs&t=510)].
* **Faker.js Incident:** A single developer abruptly deleted/blanked out a popular npm module out of frustration, instantly breaking dependency trees globally [[09:05](https://www.youtube.com/watch?v=FryxNwEeZDs&t=545)].

### **Mitigation Strategies**

* **Minimize Dependencies:** Don't reinvent the wheel, but avoid importing unnecessary libraries [[10:10](https://www.youtube.com/watch?v=FryxNwEeZDs&t=610)].
* **Version Pinning:** Lock dependency versions to prevent surprise updates [[10:55](https://www.youtube.com/watch?v=FryxNwEeZDs&t=655)].
* **Security Auditing:** Regularly monitor security advisory feeds for your tech stack [[11:12](https://www.youtube.com/watch?v=FryxNwEeZDs&t=672)].

---

## 3. Server-Side Infrastructure & Communication

### **Inter-Server TLS Encryption**

* While HTTPS secures traffic between the browser and the front-end server, **Transport Layer Security (TLS)** must also be enforced between internal distributed servers (e.g., between Flask, Redis, and Celery workers) to prevent packet snooping and injection [[13:58](https://www.youtube.com/watch?v=FryxNwEeZDs&t=838)].

### **Network-Level Access Control**

* Backend servers (databases, task queues) should be isolated from direct public internet access [[14:43](https://www.youtube.com/watch?v=FryxNwEeZDs&t=883)]. Only verified front-end proxy servers should be permitted to communicate with backend endpoints.

---

## 4. Denial of Service (DoS) and Distributed Denial of Service (DDoS)

* **What is a DoS Attack?** An attempt to make a server or network resource unavailable to its users by overwhelming it with illegitimate traffic, disrupting legitimate commerce [[15:26](https://www.youtube.com/watch?v=FryxNwEeZDs&t=926)].
* **DDoS Scale:** Modern attacks can hit servers with massive volumes of traffic (e.g., multi-terabit-per-second volumetric floods) [[17:03](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1023)]. Mitigating these attacks requires global infrastructure scrubbing providers like Cloudflare [[17:26](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1046)].

---

## 5. DevOps & Automation

* **Automated CI/CD Pipelines:** Use GitHub Actions or GitLab CI to automate code compilation, testing, staging, and production deployment [[18:43](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1123)]. Automation minimizes human error during deployment [[19:18](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1158)].
* **Secure Secret Management:** Never hardcode secret keys, JWT tokens, salts, or API credentials into version control (`git`) repositories [[25:21](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1521)]. Always inject them securely via environment variables or secret managers.

---

## 6. Password Practices (NIST Guidelines)

Modern security standards (such as those outlined by **NIST**) have updated traditional password wisdom:

* **Length Over Complexity:** Requiring uppercase letters, numbers, and special characters forces users to create hard-to-remember passwords, often leading them to write them down [[21:53](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1313)]. Instead, **passphrase length** (e.g., 4 random words) is far more critical and resilient against brute-force attacks [[21:31](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1291)].
* **No Forced Periodic Resets:** Forcing users to change passwords every 90 days encourages weak passwords and poor storage habits [[22:06](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1326)].
* **Password Hashing (One-Way Functions):** Never store raw or plaintext passwords in your database [[23:25](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1405)]. Store only cryptographic hashes generated using secure salting functions (e.g., bcrypt, Argon2) [[23:40](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1420)]. When a user logs in, hash the entered password and compare the hash, immediately discarding the plaintext input [[24:07](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1447)].

---

## 7. Effective Logging Strategies

* Logs are crucial for incident post-mortems and debugging [[26:02](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1562)].
* However, excessive, unmanaged logging can degrade server performance and inflate storage costs [[26:15](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1575)].
* **Best Practice:** Implement **log rotation** and store aggregated metric summaries using time-series analysis [[26:42](https://www.youtube.com/watch?v=FryxNwEeZDs&t=1602)].
