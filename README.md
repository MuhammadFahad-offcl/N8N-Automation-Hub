# n8n Automation Hub

Production-style n8n workflows I build for real business problems: capturing leads, cleaning data, and replying to customers on WhatsApp automatically.

Each folder is one workflow you can import straight into n8n, with its own README, architecture notes and the exported workflow JSON.

## Workflows

| # | Workflow | What it does | Stack |
| --- | --- | --- | --- |
| 01 | [Lead Intake & Qualification](01-lead-intake-pipeline) | Takes a website form submission, cleans the data, saves it to MySQL without duplicates, and sends the lead a WhatsApp confirmation in real time. | n8n · MySQL · WhatsApp Cloud API · JavaScript |

More workflows are added as I clean up client builds for public release.

## Use a workflow

1. Open your n8n instance and go to **Workflows → Import from File**.
2. Pick the `.json` file from the workflow's folder.
3. Add your own credentials (MySQL, WhatsApp Cloud API, etc.). No real credentials are stored in this repo.
4. Read that folder's README for the expected input and setup.

## Related work

- [WhatsApp Appointment Agent](https://github.com/MuhammadFahad-offcl/WhatsApp-Appointment-Agent-n8n): multimodal WhatsApp booking agent (text + voice) on n8n.
- [AI Contact Form Agent](https://github.com/MuhammadFahad-offcl/AI-Contact-Form-Agent-n8n): scrapes website contact forms, fills them with an LLM and logs results.
- [VoxFlow AI Solutions](https://voxflow.agency): the agency I run, building AI automation for businesses in Pakistan and the UAE.

## License

MIT. See each workflow folder.
