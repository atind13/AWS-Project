# Project 01 — Multi-Stage CI/CD Pipeline with AWS CodePipeline

**Track:** CI/CD & Automation  |  **Difficulty:** ⚡ Intermediate  |  **Time:** 8–12 hours

---

## 🎯 What You'll Build

A production-grade multi-stage CI/CD pipeline that automatically tests, builds, and deploys a Node.js application to EC2 using AWS-native tools — no third-party CI servers required.

---

## 📋 Objectives (All Required)

- [ ] **1.** Create an AWS CodePipeline with at least 4 stages: Source → Test → Build → Deploy
- [ ] **2.** Integrate CodeBuild for running `npm test` and `npm audit` (security scan)
- [ ] **3.** Build and push a Docker image to ECR in the Build stage
- [ ] **4.** Configure a Blue/Green deployment via CodeDeploy on an EC2 Auto Scaling Group
- [ ] **5.** Add a Manual Approval stage before production deployment (SNS email notification)
- [ ] **6.** Set up a CloudWatch alarm that triggers SNS if the pipeline fails

---

## 🏗 Architecture

```
GitHub Repository
      │
      ▼ (webhook on push to main)
AWS CodePipeline
      │
      ├── Stage 1: Source
      │   └── CodeStar Connection → GitHub
      │
      ├── Stage 2: Test
      │   └── CodeBuild (buildspec-test.yml)
      │       ├── npm install
      │       ├── npm audit --audit-level=high
      │       └── npm test (Jest)
      │
      ├── Stage 3: Build
      │   └── CodeBuild (buildspec-build.yml)
      │       ├── docker build
      │       └── docker push → ECR
      │
      ├── Stage 4: Manual Approval
      │   └── SNS → your email
      │
      └── Stage 5: Deploy
          └── CodeDeploy
              └── Blue/Green → EC2 ASG (port 80 ↔ 8080)
```

---

## 🛠 Required Files in Your Submission

```
solutions/01-cicd-codepipeline/YOUR-USERNAME/
├── README.md                  ← Required: writeup + screenshots
├── buildspec-test.yml         ← CodeBuild test stage
├── buildspec-build.yml        ← CodeBuild docker build stage
├── appspec.yml                ← CodeDeploy deployment spec
├── scripts/
│   ├── install.sh             ← BeforeInstall hook
│   ├── start.sh               ← ApplicationStart hook
│   └── stop.sh                ← ApplicationStop hook
├── terraform/                 ← (optional but encouraged)
│   ├── pipeline.tf
│   ├── codebuild.tf
│   └── codedeploy.tf
└── app/                       ← Simple Node.js app (or use the starter)
    ├── package.json
    ├── index.js
    └── __tests__/
```

---

## 🚦 Starter App

Use the starter Node.js app in `projects/01-cicd-codepipeline/starter/` or bring your own.

**Minimum requirements for the app:**
- Exposes `GET /health` returning `200 OK`
- Has at least 2 unit tests
- Has a `Dockerfile`

---

## ✅ Evaluation Rubric

| Criterion | Points | Notes |
|-----------|--------|-------|
| Pipeline has all 4+ stages | 20 | Screenshot of working pipeline |
| Tests run in CodeBuild | 15 | Show passing test output |
| Docker image pushed to ECR | 15 | Show ECR repository with image |
| Blue/Green deploy works | 20 | Show both target groups + traffic shift |
| Manual approval configured | 10 | Show approval email received |
| Pipeline failure alarm | 10 | Show CloudWatch alarm + SNS |
| README quality | 10 | Architecture, steps, screenshots |
| **Total** | **100** | |

---

## 💡 Tips

- Use `aws codepipeline start-pipeline-execution` to trigger manually during testing
- The CodeStar connection needs to be **Pending → Available** before it works — do this in the console first
- For Blue/Green: the original ASG is "blue", new deployment creates "green". Route 53 or ALB handles traffic shift.
- Set `CODEBUILD_RESOLVED_SOURCE_VERSION` as your Docker image tag for traceability

---

## 💰 Cost Estimate

| Service | Estimated Cost |
|---------|---------------|
| CodePipeline | $1/pipeline/month |
| CodeBuild | ~$0.005/build-minute (free tier: 100 min/month) |
| EC2 (t3.micro × 2) | ~$0.02/hr (stop when done!) |
| ECR | $0.10/GB stored |
| **Total for lab** | **~$2–5** |

**Cleanup:** Delete the pipeline, CodeBuild projects, CodeDeploy app, ECR repo, and EC2 instances when done.

---

## 📚 References

- [CodePipeline User Guide](https://docs.aws.amazon.com/codepipeline/latest/userguide/)
- [CodeBuild buildspec reference](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html)
- [CodeDeploy Blue/Green deployments](https://docs.aws.amazon.com/codedeploy/latest/userguide/deployments-create-console-blue-green.html)
