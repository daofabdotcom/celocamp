# Celo Camp Instructions for Testing

# Interoperable cUSD 

Background:- 
For the purpose of testing, we have created 2 issuers (Issuer A and B) and 2 users (User A and B). 
An issuer refers to a e-money issuer/solution such as Celo. User A is a user of Issuer A (and User B is a user of Issuer B). 
An Ubin escrow of USD$10,000 has been created by Issuer A in favour of Issuer B. 
User A has an account of USD $100 balance with Issuer B and User B has an account of USD $200 balance with Issuer B.  

http://localhost:8080/

# Step 1: Signing of Transaction
1. User A signs a transaction (to be sent to Issuer A)
Description: This API is to simulate user signing the transaction. In production, user will use his wallet to sign the transaction.
POST Request: /sign
Body: JSON content – unsigned transaction. Sample:
{
    "type": "TX",
    "senderId": "userA",
    "senderIssuerId": "issuerA",
    "recipientId": "userB",
    "recipientIssuerId": "issuerB",
    "ubinEscrowId": "IssuerAtoIssuerBEscrowId1",
    "amount": "500.0",
    "timestamp": "1605960991388",
    "status": "SUCCESS"
}
Response Body: JSON content – signed transaction. Sample:
{
    "type": "TX",
    "senderId": "userA",
    "senderIssuerId": "issuerA",
    "recipientId": "userB",
    "recipientIssuerId": "issuerB",
    "ubinEscrowId": "IssuerAtoIssuerBEscrowId1",
    "amount": "500.0",
    "timestamp": "1605960991388",
    "status": "SUCCESS",
    "signature": "CEeUL/0qN4ywGoCAVQsEHnG2kWoiuZtN7XqG2PNlKNAeMVXrab2UaAB6EOuYMyrZ4UgqBuOtUMigXqbKdUdRhnzEXSUeHMADXKaKBOGlhB1AiXvWDBiNFlL3/vd166wM13rGmd2oexkbowlzQj2kNPPnQT9g1vSAsDl1yxRUIleDtPGSYNIJNvdx6l/rCyLGjT9HhKhOezQkfPSFBVUuTDIOFF6ZOJ2pw8rnYs5ydD5sQSFoCgIsFxRo3DPpgR92Zx9oQgXs0FGHUObGM8Hh9vuDv4vwUV0jL6PTjZzS0kMaYfrAdppUf295u6jEYwVBPNNw7PE5WwiA+8qz4IWSsulcVcKMosPU/W7VlyeOPNjyfTHU5m3izbrQjKwR0pVavBCi07/QT8J6e/cdwOqehylmmxxgHnH9+C6lgH0PPJ0ujEw8hsYZc4FEu70dkiRMxL97aY8hkGjPZot029+93QlI2OeYtddr9BYyooQ5IdkQy6rDVpkqhUhlPtbN3yOKyGiEwaVhchX4VGJmHlb20nd9jtQZ4I2EG4CK2eQ/IKLyOGyEbG1uOIYXlIsqNsriV/TMgrLtb75iMqcWiq2vh/ChsKipccilGBppKypioZ6pIXYBAIDIXHdqBw6V4eod2l70eLc40HcraXXtw0DQcrIKSQZtueRAzEfVyWlnec4="
}

# Step 2: Proof-of-Reserve (Part 1)
GET Request: /reserve
Response Body: JSON content – Json array of all the escrows
[
    {
        "escrowId": "IssuerAtoIssuerBEscrowId1",
        "balance": 10000
    }
]

# Step 3: Proof-of-Reserve (Part 2)
Description: Issuer B checks if Issuer A has enough balance to pay Issuer B
POST Request: /check
Body: JSON content – signed transaction from User A
Response Body: Boolean 

# Step 4: Transaction Verification 
Issuer A verifies User A signed transaction
Description: Issuer A verifies the transaction by User A and if User A has sufficient balance with Issuer A.
POST Request: /verify
Body: JSON content – signed transaction from User A
Response Body: JSON content – signed confirmation receipt (Receipt signed by Issuer A to Issuer B. Its status is “SUCCESS” if User A has enough balance else “FAILED”. Receipt contains escrowId from which Issuer A is going to pay Issuer B in case of SUCCESS.)
{
    "type": "CONF",
    "senderId": "userA",
    "senderIssuerId": "issuerA",
    "recipientId": "userB",
    "recipientIssuerId": "issuerB",
    "ubinEscrowId": "IssuerAtoIssuerBEscrowId1",
    "amount": "500.0",
    "timestamp": "1605965234443",
    "status": "SUCCESS",
    "signature": "TBUUYcBKdERb9WMTlG3HxJENjeaQ4fZ+UeCQONA1mStBPEcgFY5tCwitxHuXuiS/nqQvcNnp36FHpxcTCrzu9f964SXZebRJxARGqoCHBgTJ8RaPFsJjhaj3TCv13ehsBYmxfHALKqHHl9rYrA1fPTx2IQVIqX/zydapZaGQ+CcBXf4+7QF8dwYb9Jc6duXikVLUhdVmvjs7O/jbFmGcJ1vgI/ByDAlvCLTTQNVXcabgdVKF7DTYxNk0dkVzMloNU3XBxyks4yiB86ptnic+wRF/hXd0I4j1dThfmGwUX3UUY0fUuS8aWieJkiX8ltfkVgAkznM5RgrUrbYz06M+HR8qGzy1oaKk888gG6E/Z+AZBU1QmcBjZqc5R5QL8utbCld23oEozSXbVPFe8jzKbm9aJViT4iL/NqWsc7qqt2oe75IiRH4YnDAWgtFx3dVQJr2PzNscuFQHY7or10SHrCDF9Qx0QrnQqF9l/SGY6zqMZpcsGS2ZE4Rv2RM0XGK5qlERs0HmeLONAFu7dN6MYcKorj48wYnC4IH/770iwD9FwX3yqQzlXkzQ+TdvMDWi4TqjG1roq6/ILx4b3boRKiSHSriG/TxcmhnxYP5YZRVT2C31s+pcbc+uhzW9FyInYpV3q04GLJ/KjHYjOXCCwRKzfYW9W2sNNM3D5YG1LIs="
} 

# Step 5: Confirmation Receipt
Description: Issuer B verifies the confirmation receipt signed by Issuer B. Checks the status is SUCCESS and ubin escrow Id is valid and have funds in it.
POST Request: /receipt
Body: JSON content – signed confirmation receipt from Issuer A
Response Body - Boolean


Please refer to the pdf for the Workflow diagram:- 
https://drive.google.com/open?id=1Rne7jTLRPozlcHWYYbbfgsprXH82rV_2 
