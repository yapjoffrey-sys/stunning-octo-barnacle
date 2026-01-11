{
  "name": "Presales Agent - Quote Tool",
  "nodes": [
    {
      "parameters": {
        "inputSource": "jsonExample",
        "jsonExample": "{\n    \"type\": \"inline\",\n    \"size_sqft\": 100,\n    \"price_per_sqft\": 55,\n    \"registration_fee\": 800,\n    \"subtotal\": 5500,\n    \"total\": 6300,\n    \"client_name\": \"Acme Industries\",\n    \"client_company\": \"Acme Corp\",\n    \"client_email\": \"contact@acmecorp.com\",\n    \"client_phone\": \"(555) 123-4567\"\n }"
      },
      "type": "n8n-nodes-base.executeWorkflowTrigger",
      "typeVersion": 1.1,
      "position": [
        -1104,
        16
      ],
      "id": "e43b4d7c-a633-4395-85fe-d5ece987756b",
      "name": "When Executed by Another Workflow"
    },
    {
      "parameters": {
        "authentication": "oAuth2",
        "resource": "file",
        "operation": "list",
        "owner": {
          "__rl": true,
          "value": "tobiaszwingmann",
          "mode": "list",
          "cachedResultName": "tobiaszwingmann",
          "cachedResultUrl": "https://github.com/tobiaszwingmann"
        },
        "repository": {
          "__rl": true,
          "value": "build_AI_agents_with-n8n-4103420-main",
          "mode": "list",
          "cachedResultName": "build_AI_agents_with-n8n-4103420-main",
          "cachedResultUrl": "https://github.com/tobiaszwingmann/build_AI_agents_with-n8n-4103420-main"
        },
        "filePath": "tools/quotes/template"
      },
      "type": "n8n-nodes-base.github",
      "typeVersion": 1.1,
      "position": [
        -880,
        -96
      ],
      "id": "d8534cb0-d727-467a-b896-d26f5c615f9b",
      "name": "List files",
      "webhookId": "ee869df5-4ad0-43ef-8282-c93f4978647c",
      "credentials": {
        "githubOAuth2Api": {
          "id": "rqkMAdMVwbOGDQB4",
          "name": "GitHub account"
        }
      }
    },
    {
      "parameters": {
        "authentication": "oAuth2",
        "resource": "file",
        "operation": "get",
        "owner": {
          "__rl": true,
          "value": "tobiaszwingmann",
          "mode": "list",
          "cachedResultName": "tobiaszwingmann",
          "cachedResultUrl": "https://github.com/tobiaszwingmann"
        },
        "repository": {
          "__rl": true,
          "value": "build_AI_agents_with-n8n-4103420-main",
          "mode": "list",
          "cachedResultName": "build_AI_agents_with-n8n-4103420-main",
          "cachedResultUrl": "https://github.com/tobiaszwingmann/build_AI_agents_with-n8n-4103420-main"
        },
        "filePath": "={{ $json.path }}",
        "asBinaryProperty": false,
        "additionalParameters": {}
      },
      "type": "n8n-nodes-base.github",
      "typeVersion": 1.1,
      "position": [
        -656,
        -96
      ],
      "id": "f8fd19ea-b130-45ff-80fb-12129f84bcb1",
      "name": "Get a file",
      "webhookId": "d27df519-362c-47f6-9b1c-b633d15bb7ef",
      "credentials": {
        "githubOAuth2Api": {
          "id": "rqkMAdMVwbOGDQB4",
          "name": "GitHub account"
        }
      }
    },
    {
      "parameters": {
        "url": "={{ $json.download_url }}",
        "authentication": "predefinedCredentialType",
        "nodeCredentialType": "githubOAuth2Api",
        "options": {}
      },
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [
        -432,
        -96
      ],
      "id": "d26f0db4-363e-4048-b66e-6c8b45e119b4",
      "name": "HTTP Request",
      "credentials": {
        "githubOAuth2Api": {
          "id": "rqkMAdMVwbOGDQB4",
          "name": "GitHub account"
        }
      }
    },
    {
      "parameters": {},
      "type": "n8n-nodes-base.merge",
      "typeVersion": 3.2,
      "position": [
        -208,
        16
      ],
      "id": "6273c0ea-defe-4707-af49-c2695f2dc684",
      "name": "Merge"
    },
    {
      "parameters": {
        "jsCode": "// n8n Code node (JavaScript)\n// Returns one item with dynamic date fields and quote ID\n\n// --- Dynamic fields ---\nconst now = new Date();\n\n// Format date as YYYY-MM-DD\nconst formatDate = (d) => d.toISOString().slice(0, 10);\n\n// Current date\nconst quote_date = formatDate(now);\n\n// Valid until = 30 days later\nconst valid_until = formatDate(new Date(now.getTime() + 30 * 24 * 60 * 60 * 1000));\n\n// Quote ID derived from timestamp (YYMMDD-HHMMSS)\nconst pad = (n) => n.toString().padStart(2, \"0\");\nconst quote_id = `Q-${now.getFullYear().toString().slice(-2)}${pad(now.getMonth()+1)}${pad(now.getDate())}-${pad(now.getHours())}${pad(now.getMinutes())}${pad(now.getSeconds())}`;\n\n// --- Static fields ---\nreturn [\n  {\n    json: {\n      quote_id,\n      quote_date,\n      valid_until,\n\n      client_name: $input.last().json.client_name,\n      client_company: $input.last().json.client_company,\n      client_email: $input.last().json.client_email,\n      client_phone: $input.last().json.client_phone,\n\n      org_name: \"Tech Innovate Sales\",\n      org_email: \"sales@techinnovate2026.com\",\n      org_phone: \"(415) 555-0123\",\n\n      type: $input.last().json.type.toUpperCase(),\n      size_sqft: $input.last().json.size_sqft,\n      price_per_sqft: $input.last().json.price_per_sqft,\n      registration_fee: $input.last().json.registration_fee,\n      subtotal: $input.last().json.subtotal,\n      total: $input.last().json.total\n    }\n  }\n];\n\n"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        -432,
        112
      ],
      "id": "14682140-980b-423f-96d9-27cbfeb2b814",
      "name": "Define variables"
    },
    {
      "parameters": {
        "jsCode": "// n8n Code node (JavaScript)\n// Expects: \n//   item[0].json.data -> HTML string with {{placeholders}}\n//   item[1].json -> JSON object with data to fill in\n// Returns: one item with rendered HTML\n\nconst htmlTemplate = $input.first().json.data;\nconst vars = $input.last().json;\n\n// Replace {{key}} placeholders in the HTML\nlet rendered = htmlTemplate;\n\nfor (const [key, value] of Object.entries(vars)) {\n  const regex = new RegExp(`{{\\\\s*${key}\\\\s*}}`, 'g');\n  rendered = rendered.replace(regex, value ?? '');\n}\n\n// Return the filled HTML\nreturn [\n  {\n    json: {\n      html: rendered\n    }\n  }\n];\n"
      },
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [
        16,
        16
      ],
      "id": "5c40b392-9880-4049-b34a-12f97ca186a2",
      "name": "Update Quote"
    },
    {
      "parameters": {
        "authentication": "oAuth2",
        "resource": "file",
        "owner": {
          "__rl": true,
          "value": "tobiaszwingmann",
          "mode": "list",
          "cachedResultName": "tobiaszwingmann",
          "cachedResultUrl": "https://github.com/tobiaszwingmann"
        },
        "repository": {
          "__rl": true,
          "value": "build_AI_agents_with-n8n-4103420-main",
          "mode": "list",
          "cachedResultName": "build_AI_agents_with-n8n-4103420-main",
          "cachedResultUrl": "https://github.com/tobiaszwingmann/build_AI_agents_with-n8n-4103420-main"
        },
        "filePath": "=tools/quotes/new/{{ $('Define variables').first().json.quote_id}}.html",
        "fileContent": "={{ $json.html }}",
        "commitMessage": "=Added {{ $('Define variables').first().json.quote_id}}"
      },
      "type": "n8n-nodes-base.github",
      "typeVersion": 1.1,
      "position": [
        240,
        16
      ],
      "id": "1e143a70-3320-427d-ac8d-495abb99b54a",
      "name": "Create a file",
      "webhookId": "d27df519-362c-47f6-9b1c-b633d15bb7ef",
      "credentials": {
        "githubOAuth2Api": {
          "id": "rqkMAdMVwbOGDQB4",
          "name": "GitHub account"
        }
      }
    },
    {
      "parameters": {
        "assignments": {
          "assignments": [
            {
              "id": "ff5daa32-afd1-47e4-a71a-b51d05d76550",
              "name": "download_url",
              "value": "=https://htmlpreview.github.io/?{{ $json.content.download_url }}",
              "type": "string"
            }
          ]
        },
        "options": {}
      },
      "type": "n8n-nodes-base.set",
      "typeVersion": 3.4,
      "position": [
        464,
        16
      ],
      "id": "0ea23f94-02cd-4098-80a1-c6b4fe4b5b5d",
      "name": "Edit Fields"
    }
  ],
  "pinData": {
    "When Executed by Another Workflow": [
      {
        "json": {
          "type": "inline",
          "size_sqft": 100,
          "price_per_sqft": 55,
          "registration_fee": 800,
          "subtotal": 5500,
          "total": 6300,
          "client_name": "Acme Industries",
          "client_company": "Acme Corp",
          "client_email": "contact@acmecorp.com",
          "client_phone": "(555) 123-4567"
        }
      }
    ]
  },
  "connections": {
    "When Executed by Another Workflow": {
      "main": [
        [
          {
            "node": "List files",
            "type": "main",
            "index": 0
          },
          {
            "node": "Define variables",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "List files": {
      "main": [
        [
          {
            "node": "Get a file",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Get a file": {
      "main": [
        [
          {
            "node": "HTTP Request",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "HTTP Request": {
      "main": [
        [
          {
            "node": "Merge",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Merge": {
      "main": [
        [
          {
            "node": "Update Quote",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Define variables": {
      "main": [
        [
          {
            "node": "Merge",
            "type": "main",
            "index": 1
          }
        ]
      ]
    },
    "Update Quote": {
      "main": [
        [
          {
            "node": "Create a file",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Create a file": {
      "main": [
        [
          {
            "node": "Edit Fields",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  },
  "versionId": "66ba6d41-fdb3-4c13-9b94-e17ce8102521",
  "meta": {
    "instanceId": "94e1edcc34a91a151fc2ec1b99597068a304e37fdb946fc806f00f97776fce3a"
  },
  "id": "xv2TJBGsA6t0gsds",
  "tags": []
}# stunning-octo-barnacle
