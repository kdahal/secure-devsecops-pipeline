1. Repository Setup Inputs:
# GitHub Repo: Create a repo with .github/workflows/devsecops.yml for the pipeline definition.
# Branching: Use main for production, develop for staging.
# Secrets in GitHub: Store Vault URL, token, and AWS creds as repo secrets (e.g., VAULT_ADDR, AWS_ACCESS_KEY_ID).

2. HashiCorp Vault Configuration:
# Install Vault (self-hosted or cloud)
# Policy File (hvn-policy.hcl)

path "secret/data/devsecops/*" {
  capabilities = ["read", "create", "update"]
}

3. Secret Injection Script (in pipeline YAML):
# Fetch secret from Vault
vault kv get -field=password secret/devsecops/db-pass > db_pass.txt
# Use in Docker build: ENV DB_PASS=$(cat db_pass.txt)
# Cleanup: rm db_pass.txt

# GitHub Actions Pipeline YAML (.github/workflows/devsecops.yml)

4. Security Scanning Thresholds (as Inputs to Gates):
# SonarQube: Quality Gate pass if bugs <5, vulnerabilities <3.
# Trivy: Block if HIGH/CRITICAL vulns >0.
# OWASP ZAP: Alert score < "Low".

5. Metrics for 50% Cycle Reduction:
# Pre-Implementation: Manual testing + approval: 240 min.
# Post-Implementation: Parallel stages + automation: 120 min.
# Tracked via GitHub Actions insights and custom Prometheus queries (e.g., histogram_quantile(0.95, rate(pipeline_duration[5m]))).

Results and Lessons Learned
# Quantitative Wins: 50% faster deploys (from 4 to 2 hours); 100% secret encryption; zero P1 incidents in 6 months.
# Qualitative Wins: Shift-left security mindset; easier onboarding for devs.
# Challenges & Improvements: Initial Vault setup took 2 weeks—recommend Vault Operator for K8s. Future: Add AI-driven anomaly detection.
# Scalability: Handles 10+ microservices; extendable to multi-cloud.