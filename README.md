# Cedarline Trading

An Odoo ERP and n8n integration lab for a fictional building-materials distributor. The lab follows a box of porcelain tiles from supplier pricing and purchasing through receipt, sale, delivery, invoicing, and payment. It also tests reading and writing Odoo records through the JSON-2 API.

The Odoo workflows and both n8n automations were verified in the lab. Power BI reporting is the next phase.

![Cedarline's tile opportunity in the Qualified stage of the Odoo CRM pipeline](screenshots/odoo/crm-qualified-opportunity.png)

## Odoo walkthrough

| Workflow | Verified result | Screenshots |
| --- | --- | --- |
| Company setup | Cedarline uses the Lebanon localization, LBP, and 11% VAT on the sample transactions. | [Accounting settings](screenshots/odoo/accounting-localization.png) |
| Sales and delivery | Juniper’s confirmed order **S00001** was for five boxes. **WH/OUT/00001** recorded shipment of all five. The customer invoice was **5,550,000 LBP** including VAT and reached **Paid** after simulated bank matching. | [Sales order](screenshots/odoo/sales-order-s00001.png) · [Delivery](screenshots/odoo/customer-delivery.png) · [Paid invoice](screenshots/odoo/paid-customer-invoice.png) |
| Purchasing and receipt | Cedarline ordered 20 boxes from Bekaa Tile Supply at **700,000 LBP** each. **WH/IN/00001** recorded the receipt. The vendor bill reached **Paid** after matching the simulated withdrawal. | [Purchase order](screenshots/odoo/purchase-order-p00001.png) · [Receipt](screenshots/odoo/warehouse-receipt.png) · [Paid vendor bill](screenshots/odoo/paid-vendor-bill.png) |
| Accounting and valuation | After the first sale, the remaining 15 boxes were valued at **10,500,000 LBP**. The posted stock closing entry moved that value into inventory. The P&L snapshot at that point showed **1,500,000 LBP** net profit. | [Invoice journal](screenshots/odoo/customer-invoice-journal.png) · [Stock closing entry](screenshots/odoo/stock-closing-journal.png) · [P&L snapshot](screenshots/odoo/profit-and-loss.png) |
| Discounts and payment terms | A 10% line discount brought **S00002** to **4,995,000 LBP** including VAT. Its posted invoice split the amount across two due dates. | [Discounted quote](screenshots/odoo/sales-line-discount.png) · [Installments](screenshots/odoo/installment-due-dates.png) |
| Stock locations and replenishment | The stock report showed **7 boxes** in main storage and **3** in the showroom. A manual rule with a minimum of 12 and a maximum of 20 generated draft RFQ **P00002** for 10 boxes. | [Stock by location](screenshots/odoo/stock-by-location.png) · [Manual rule](screenshots/odoo/manual-reordering-rule.png) · [Draft RFQ](screenshots/odoo/replenishment-rfq-p00002.png) |
| Studio and user access | A custom **Delivery Instructions** field saved a note on Juniper’s contact. The **Warehouse Staff** test login opened the completed internal transfer to the showroom. | [Studio field](screenshots/odoo/studio-delivery-instructions.png) · [Warehouse permissions](screenshots/odoo/warehouse-user-access.png) · [Transfer access](screenshots/odoo/warehouse-staff-internal-transfer.png) |
| Project and Fleet | The Showroom Setup task was closed. Cedarline’s sample delivery van was registered and its **24,500 km** odometer reading was logged. | [Project task](screenshots/odoo/project-task-closed.png) · [Fleet record](screenshots/odoo/fleet-odometer.png) |

The full screenshot set also covers contact setup, product pricing, journal details, and stock valuation. [Browse the Odoo screenshots](screenshots/odoo/)

## n8n integration

Both workflows use a manual trigger and send JSON to a named Odoo method. Odoo uses HTTP `POST` for both method calls. The method controls whether the call reads or writes data. [Odoo JSON-2 documentation](https://www.odoo.com/documentation/19.0/developer/reference/external_api.html)

| Workflow | Odoo method | Verified result |
| --- | --- | --- |
| [Read and filter sales orders](workflows/read-and-filter-sales-orders.json) | `sale.order/search_read` | Read the confirmed orders and kept **S00001** at **5,550,000 LBP** using a **5,000,000 LBP** threshold. **S00002** at **4,995,000 LBP** fell below the threshold. |
| [Create a test contact](workflows/create-test-contact.json) | `res.partner/create` | Returned contact ID **14** in n8n. The sample name and email were verified separately in [Odoo Contacts](screenshots/odoo/contact-verified.png). |

### Run the workflows

1. Use an n8n instance and an Odoo database with JSON-2 API access. Generate an Odoo API key for an account with the permissions needed for the workflow.
2. In the n8n workflow editor, choose **Import from File** and import one of the JSON exports above. [n8n import guide](https://docs.n8n.io/build/manage-workflows/export-and-import)
3. In the HTTP Request node, replace `https://odoo.example.com` with your Odoo instance URL. Keep the method path and select a **Bearer Auth** credential containing your Odoo API key.
4. Execute the workflow manually and check the output against your Odoo records. The sales workflow reads up to ten confirmed orders and compares totals including tax. The contact workflow creates a new record each time, so use it with sample data.

## Repository layout

```text
screenshots/
  odoo/                  Screenshots from the Cedarline Odoo lab
workflows/
  create-test-contact.json
  read-and-filter-sales-orders.json
```

## Power BI next

The reporting phase will use Cedarline’s sales, purchasing, and inventory data. It will cover Power Query transformations, table keys and relationships, DAX measures, report filters, reconciliation to source totals, and refresh instructions.

## Reference

Jalal Al Arab’s Odoo trading automation walkthrough informed the scope of the Odoo exercises. This project uses original Cedarline sample data, screenshots, and workflows. The n8n integration uses JSON-2, and Power BI extends the walkthrough.
