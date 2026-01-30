# InsideOut Troubleshooting Guide

This guide helps you resolve common issues when using InsideOut for infrastructure design and deployment.

## Workflow Issues

### "Session not terraform-ready"

**Problem:** You called `tfgenerate` but got an error saying the session isn't ready.

**Solution:**
1. Make sure `convoreply` has completed successfully
2. Check that Riley has finished the design conversation
3. Look for `terraform_ready=true` in the `convoreply` response metadata
4. Use `convostatus` to check the current phase

**Example Fix:**
```
1. convoreply: "Are we ready for Terraform generation?"
2. Wait for Riley's response with terraform_ready=true
3. Then call tfgenerate
```

### Riley Keeps Asking the Same Question

**Problem:** Riley repeatedly asks the same question or seems stuck.

**Causes:**
- The AI is answering Riley's questions instead of forwarding them to you
- You're not providing specific enough answers
- There's a misunderstanding about requirements

**Solutions:**
1. **Answer Riley directly** - Don't let the AI answer for you
2. **Be more specific** - Instead of "small scale", say "500 concurrent users"
3. **Ask for clarification** - "What specific information do you need?"

**Example:**
```
❌ Bad: AI responds "The user wants a small database"
✅ Good: Forward to user "Riley asks: How much data do you expect to store?"
```

### Conversation Timeouts

**Problem:** `convoreply` times out before Riley responds.

**Solutions:**
1. **Use `convoawait`** to continue waiting for the response
2. **Increase timeout** - Set timeout to 50-55 seconds for complex questions
3. **Break down complex requests** - Ask simpler, more focused questions

**Example:**
```
1. convoreply times out after 50 seconds
2. Use convoawait with the same session_id
3. Wait for Riley's response
```

### "Invalid session_id" Errors

**Problem:** Tools return "invalid session_id" errors.

**Causes:**
- Using a made-up session ID
- Session ID format is wrong
- Session has expired

**Solutions:**
1. **Always use the session_id from `convoopen`**
2. **Check format** - Must start with `sess_v2_`
3. **Don't guess or make up session IDs**

**Example:**
```
✅ Correct: sess_v2_abc123def456
❌ Wrong: session_123 or my-session
```

## Terraform Generation Issues

### Download URL Fails

**Problem:** The curl/unzip command from `tfgenerate` fails.

**Causes:**
- Sandbox environment restrictions
- Network connectivity issues
- Platform doesn't support curl/unzip

**Solutions:**
1. **Use fallback mode** - Call `tfgenerate` with `include_code: true`
2. **Try alternative download methods** - wget instead of curl
3. **Check network permissions** - Ensure outbound HTTPS is allowed

**Example:**
```
1. tfgenerate returns download URL
2. curl command fails
3. Retry: tfgenerate with include_code: true
4. Get full Terraform code inline
```

### "No Terraform files generated"

**Problem:** `tfgenerate` succeeds but no files are created.

**Causes:**
- Session doesn't have complete design
- Missing required components
- Configuration is incomplete

**Solutions:**
1. **Check session status** - Use `convostatus` to see what's missing
2. **Complete the design** - Ensure components, config, and pricing are done
3. **Ask Riley** - "What's needed to generate Terraform?"

## Deployment Issues

### "No deployment found"

**Problem:** `tfstatus` or `tflogs` says no deployment found.

**Causes:**
- `tfdeploy` hasn't been called yet
- Deployment failed to start
- Wrong session ID

**Solutions:**
1. **Start deployment** - Call `tfdeploy` first
2. **Check deployment status** - Use `tfstatus` to see if it started
3. **Verify session ID** - Make sure you're using the correct session

### Deployment Stuck or Slow

**Problem:** Deployment takes longer than expected or appears stuck.

**Normal Behavior:**
- Terraform deployments typically take 15-30 minutes
- Some resources (like RDS) can take 10+ minutes alone
- EKS clusters can take 15-20 minutes

**Monitoring:**
1. **Use `tflogs`** to see real-time progress
2. **Check for errors** in the log output
3. **Be patient** - Infrastructure provisioning is inherently slow

**Example Log Progression:**
```
1. "Initializing Terraform..."
2. "Planning infrastructure changes..."
3. "Creating VPC..."
4. "Creating subnets..."
5. "Creating RDS instance..." (this takes 10+ minutes)
6. "Creating EKS cluster..." (this takes 15+ minutes)
7. "Deployment complete!"
```

### Deployment Failures

**Problem:** Deployment fails with errors in `tflogs`.

**Common Causes:**
1. **Quota limits** - Not enough vCPU/IP quota in the region
2. **Permission issues** - Missing IAM permissions
3. **Resource conflicts** - Name collisions or existing resources
4. **Configuration errors** - Invalid instance types or regions

**Solutions:**
1. **Check quotas** - Verify you have sufficient quota in the target region
2. **Review permissions** - Ensure your cloud account has necessary permissions
3. **Try different region** - Some regions have better resource availability
4. **Simplify configuration** - Start with smaller instance types

## Inspection Issues

### "Cannot inspect - no deployment"

**Problem:** `awsinspect` or `gcpinspect` fails saying no deployment found.

**Prerequisites:**
- Deployment must be completed successfully
- Use `tfstatus` to confirm deployment is complete
- Wait for all resources to be fully provisioned

**Solutions:**
1. **Confirm deployment** - Check `tfstatus` shows "completed"
2. **Wait for propagation** - Resources may take a few minutes to be discoverable
3. **Check permissions** - Ensure inspection has read permissions

### Inspection Returns Empty Results

**Problem:** Inspection tools run but return no resources.

**Causes:**
- Resources are still being created
- Wrong region or project
- Resources were created with different names

**Solutions:**
1. **Wait longer** - Some resources take time to appear in APIs
2. **Check all regions** - Resources might be in a different region
3. **Verify deployment logs** - Confirm resources were actually created

## Cost and Pricing Issues

### "Pricing estimates seem wrong"

**Problem:** Cost estimates don't match expectations.

**Causes:**
- Estimates are for full month usage
- Includes all components (compute, storage, networking)
- May include high-availability configurations

**Understanding Costs:**
1. **Monthly estimates** - Costs are per month, not per hour
2. **All-inclusive** - Includes compute, storage, data transfer, backups
3. **Production-ready** - Includes HA, monitoring, security features

**Cost Optimization:**
1. **Ask Riley** - "Can we reduce costs while maintaining functionality?"
2. **Review components** - Remove unnecessary services
3. **Adjust sizing** - Use smaller instances for development

### "Budget exceeded"

**Problem:** Estimated costs are higher than your budget.

**Solutions:**
1. **Discuss with Riley** - "My budget is $X/month, what can we adjust?"
2. **Reduce scope** - Start with core features, add others later
3. **Use development sizing** - Smaller instances, single-AZ deployments
4. **Consider serverless** - Pay-per-use pricing models

## Performance Issues

### Slow Response Times

**Problem:** InsideOut tools are responding slowly.

**Causes:**
- High server load
- Complex infrastructure designs
- Network latency

**Solutions:**
1. **Increase timeouts** - Use 50-55 second timeouts for complex operations
2. **Simplify requests** - Break complex designs into smaller steps
3. **Retry if needed** - Temporary server issues usually resolve quickly

### "Request timeout" Errors

**Problem:** Tools timeout before completing.

**Solutions:**
1. **Use longer timeouts** - Set timeout parameter to 50-55 seconds
2. **Use `convoawait`** for conversation timeouts
3. **Break down requests** - Ask simpler questions

## Authentication and Permissions

### "Access denied" Errors

**Problem:** Getting permission errors during deployment or inspection.

**AWS Solutions:**
1. **Check IAM permissions** - Ensure your account has necessary permissions
2. **Verify regions** - Some services aren't available in all regions
3. **Check service quotas** - You may have hit service limits

**GCP Solutions:**
1. **Check IAM roles** - Ensure proper roles are assigned
2. **Enable APIs** - Required APIs must be enabled in your project
3. **Verify billing** - Billing account must be active

## Getting Help

### Use the Help Tool

```
Call the help tool for comprehensive workflow guidance
```

### Check Current Status

```
Use convostatus to see:
- Current design phase
- Selected components
- Configuration details
- What's needed next
```

### Debug with DevTools (if available)

```
If MCP_DEVTOOLS_ENABLED is set, use devtools for:
- Session debugging
- Raw message dumps
- System status checks
```

## Prevention Tips

### Best Practices

1. **Always start with `convoopen`** - Don't skip the introduction
2. **Answer Riley's questions directly** - Don't let AI answer for you
3. **Be specific about requirements** - Provide concrete numbers and details
4. **Check status regularly** - Use `convostatus` to track progress
5. **Monitor deployments** - Use `tflogs` to watch deployment progress

### Common Mistakes to Avoid

1. **Don't guess session IDs** - Always use the ID from `convoopen`
2. **Don't skip the conversation** - Complete the design before generating Terraform
3. **Don't rush deployments** - Infrastructure takes time to provision
4. **Don't ignore errors** - Check logs when things fail

### When to Ask for Help

- Riley seems confused or stuck
- Deployment fails repeatedly
- Cost estimates are much higher than expected
- You're not sure what information Riley needs

Remember: InsideOut is designed to guide you through complex infrastructure decisions. When in doubt, ask Riley for clarification or use the `help` tool for detailed guidance.