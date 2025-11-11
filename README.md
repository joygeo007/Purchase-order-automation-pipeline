# Purchase-order-automation-pipeline

**Automated Purchase Order Data Extraction System**

A self-hosted, AI-powered solution for extracting structured data from purchase orders in various formats with high accuracy validation. Built using open-source tools (Unstract + n8n) to automate the end-to-end workflow from email receipt to spreadsheet population.

## Overview

This project addresses the challenge of processing purchase orders from multiple vendors with different document formats. Traditional manual data entry is error-prone and time-consuming, while commercial solutions are expensive and lack customisation. This system provides a cost-effective, accurate alternative with built-in validation layers to ensure data integrity.

## Architecture

**Email → Extract → Validate → Route → Store**

- **n8n**: Orchestrates the workflow - monitors Gmail, handles document routing, performs validation, and updates Google Sheets
- **Unstract**: Performs AI-powered document extraction using LLM technology with built-in hallucination detection
- **GCP**: Self-hosted infrastructure for complete control and data privacy
- **Multi-layer validation**: Ensures accuracy through field completeness checks, format validation, business rules, and confidence scoring

## Key Features

- 📧 **Automated Email Monitoring**: Watches Gmail inbox for purchase orders
- 🤖 **AI-Powered Extraction**: Uses LLMs (Claude/GPT-4) via Unstract to extract structured data
- ✅ **Multi-Layer Validation**: Implements comprehensive validation rules to catch errors
- 🎯 **Confidence-Based Routing**: Auto-approves high-confidence extractions, flags uncertain ones for review
- 📊 **Dual-Sheet System**: Separates auto-processed POs from those requiring human review
- 🔒 **Self-Hosted**: Complete data privacy and control
- 💰 **Cost-Effective**: ~$45-85/month vs $200-500/month for commercial solutions
- 🔧 **Vendor-Specific Templates**: Customizable extraction prompts for different PO formats
- 📈 **Monitoring & Metrics**: Tracks extraction accuracy and system performance

## Technology Stack

- **Workflow Automation**: n8n (self-hosted)
- **Document Processing**: Unstract (open-source)
- **LLM**: Claude Sonnet 4 / GPT-4
- **OCR**: LLMWhisperer
- **Infrastructure**: Google Cloud Platform (Compute Engine)
- **Storage**: Google Sheets
- **Notifications**: Email/Slack (configurable)

## Workflow

1. **Trigger**: n8n monitors Gmail for incoming purchase orders
2. **Extract**: PDF attachment is sent to Unstract API
3. **Process**: Unstract uses LLM to extract key fields (PO number, vendor info, line items, totals, dates, terms)
4. **Validate**: n8n runs validation checks:
   - Field completeness
   - Data format validation
   - Mathematical verification (line items sum to total)
   - Business rules compliance
   - Confidence score evaluation
5. **Route**: 
   - High confidence + validation pass → Auto-approve sheet
   - Low confidence or validation fail → Review required sheet + notification
6. **Review**: Human reviews flagged items, approves/corrects data
7. **Learn**: System improves accuracy with vendor-specific templates over time

## Validation Rules

### Required Field Checks
- PO Number (unique, non-empty)
- Vendor information (name, contact)
- Line items (product, quantity, unit price, total)
- Dates (PO date, delivery date)
- Totals (subtotal, tax, grand total)

### Format Validation
- Dates in ISO format
- Numbers properly formatted
- Email addresses valid
- Phone numbers standardized

### Business Logic
- Quantities > 0
- Prices within reasonable ranges
- Line item totals = quantity × unit price
- Subtotal + tax + shipping = grand total
- PO number not duplicate

### Confidence Thresholds
- ≥98% confidence + all validations pass → Auto-approve
- 90-97% confidence → Quick review
- <90% confidence → Full manual review

## System Architecture
```
┌─────────────────┐
│  Gmail Inbox    │
│  (Trigger)      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  n8n Workflow   │
│  - Extract PDF  │
│  - Route to API │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Unstract API   │
│  - OCR Extract  │
│  - LLM Process  │
│  - Hallucination│
│    Detection    │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Validation     │
│  Engine (n8n)   │
│  - Field checks │
│  - Format valid │
│  - Math verify  │
│  - Confidence   │
└────────┬────────┘
         │
         ├─── High Confidence ──► Google Sheets (Auto)
         │                        + Audit Log
         │
         └─── Low Confidence ──► Google Sheets (Review)
                                 + Notification
                                 + Human Review
```

## Key Technical Decisions

### Why Unstract?
- **LLMChallenge**: Uses two separate LLMs to cross-validate extractions, catching hallucinations early
- **AI Stack Agnostic**: Flexibility to use any LLM provider (Claude, GPT-4, open-source models)
- **Prompt Studio**: Visual interface for creating and testing extraction prompts without coding
- **Open Source**: Full customization and no vendor lock-in

### Why n8n?
- **Visual Workflow Editor**: Easy to build and modify complex automation logic
- **Self-Hostable**: Complete control over execution and data
- **Extensive Integrations**: Native nodes for Gmail, Google Sheets, HTTP APIs, validation logic
- **Error Handling**: Built-in retry logic and error notification

### Why Self-Hosted on GCP?
- **Data Privacy**: Sensitive PO data never leaves your infrastructure
- **Cost Control**: Predictable monthly costs vs per-transaction pricing
- **Customization**: Full access to modify and extend functionality
- **Performance**: Dedicated resources for consistent processing speed


## Error Handling & Edge Cases

### Handled Scenarios
- Poor quality scans → OCR enhancement + manual review flag
- Missing required fields → Validation failure + vendor notification
- Ambiguous data → Low confidence score + human review
- New vendor formats → Template learning mode
- Duplicate PO numbers → Automatic rejection + alert
- Mathematical inconsistencies → Validation failure + highlight discrepancies

### Monitoring & Alerts
- Real-time notifications for extraction failures
- Daily summary of processing stats
- Weekly accuracy reports
- Monthly vendor-specific performance analysis
- Alert thresholds for unusual patterns (volume spikes, accuracy drops)

## Use Cases

This system is ideal for:
- **Procurement departments** processing 40+ POs monthly
- **Small to medium businesses** with multiple vendors
- **Accounting teams** needing accurate PO data for invoicing
- **Operations teams** tracking order fulfillment
- **Finance departments** requiring audit trails

## Limitations & Design Choices

- **Not 100% automated**: Human review required for ~10-20% of POs by design (safety measure)
- **Learning period**: Accuracy improves over first 4-8 weeks as vendor templates are built
- **Quality dependency**: Requires reasonably clear scans (same as human reviewers)
- **Structured documents**: Optimized for standard PO formats, not unstructured correspondence
- **Human-in-the-loop**: Intentionally designed to flag uncertain extractions rather than guess


## Project Impact

- **Business Value**: Eliminated manual data entry bottleneck
- **Cost Savings**: $1,800-5,000 annual savings on software licensing
- **Time Savings**: ~5 minutes per PO × volume = significant efficiency gains
- **Accuracy Improvement**: Reduced errors from 5% (manual) to <1% (automated)
- **Scalability**: System handles volume increases without linear cost growth
- **Audit Trail**: Complete logging for compliance and troubleshooting

## Acknowledgments

- [Unstract](https://github.com/Zipstack/unstract) - Open-source document extraction platform
- [n8n](https://n8n.io/) - Workflow automation platform
- [Anthropic Claude](https://www.anthropic.com/) - LLM for high-accuracy extraction
