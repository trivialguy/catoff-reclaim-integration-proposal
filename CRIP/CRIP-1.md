| proposal | title              | description                   | author                     | discussions-to | status | type        | category | created    | requires |
|----------|--------------------|-------------------------------|----------------------------|----------------|--------|-------------|----------|------------|----------|
| CRIP-1   | Duolingo Integration | Integration with Duolingo API to validate first language learned, total XP, username and from language | Kshitij Jha <jhakshitij.2003@gmail.com> |                | Draft  | Integration | CRIP     | 2024-06-11 |          |

## Title

Duolingo Integration

## Introduction

This proposal outlines the integration of Duolingo as a data provider for the Catoff-Reclaim integration project. The integration aims to retrieve and process user's Total XP, first language learnt, username and from language data from Duolingo, to be used within the Catoff platform. This will enable users to validate their Duolingo XPs, from language and first language learnt, and use them for various challenges and verifications on Catoff.

## External APIs Needed

- Duolingo API: https://www.duolingo.com/2017-06-30/

## Use Cases

1. **User Verification**: Verify the XP of users on Duolingo.
2. **Challenge Participation**: Allow users to participate in challenges that require proof of Duolingo XP.
3. **Skill Assessment**: Assess users' linguistic skills and dedication based on their Duolingo XP.

## Data Provider

- **Name**: Duolingo UserName - Fix
- **Hash Value**: 0x6cee0c60289814031f1eca7501fc86263a8b26022d6b2441a56e12525d27cc6d

## Code Snippet

Below is a code snippet that demonstrates the key parts of the Duolingo integration. The full implementation should follow the service file template.

**`services/duolingoService.js`**

```javascript
const axios = require('axios')
const { ReclaimServiceResponse } = require('../utils/reclaimServiceResponse')

exports.processDuolingoData = async (proof, providerName) => {
    const duolingoUsername = JSON.parse(proof[0].claimData.context)
        .extractedParameters.userName
    const lastUpdateTimeStamp = proof[0].claimData.timestampS

    const totalXp = await getUserXp(duolingoUsername)

    return new ReclaimServiceResponse(
        providerName,
        lastUpdateTimeStamp,
        duolingoUsername,
        totalXp,
        proof[0]
    )
}

const getUserXp = async username => {
    const url = `https://www.duolingo.com/2017-06-30/users?username=${username}`;
    console.log(url);
    const headers = {
        'Authorization': `Basic ${Buffer.from(`${process.env.username}:${process.env.password}`).toString('base64')}`,
        'Accept': 'application/json',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/125.0.0.0 Safari/537.36',
        'Content-Type': 'application/json'
    };
    try {

        const response = await axios.get(url, { headers })
        console.log(
            `Total XP gained by ${username}: ${response.data.users[0].totalXp}`
        )
        return response.data.total_count;
    }
    catch (error) {
        console.log("error: ", error);
        return null;
    }

}


```
