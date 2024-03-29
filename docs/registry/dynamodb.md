# The DynamoDB registry

The DynamoDB registry stores DNS record metadata in an AWS DynamoDB table.

## The DynamoDB Table

By default, the DynamoDB registry stores data in the table named `external-dns`.
A different table may be specified using the `--dynamodb-table` flag.
A different region may be specified using the `--dynamodb-region` flag.

The table must have a partition (hash) key named `k` and string type.
The table must not have a sort (range) key.

## IAM permissions

The ExternalDNS Role must be granted the following permissions:

```json
    {
      "Effect": "Allow",
      "Action": [
        "DynamoDB:DescribeTable",
        "DynamoDB:PartiQLDelete",
        "DynamoDB:PartiQLInsert",
        "DynamoDB:PartiQLUpdate",
        "DynamoDB:Scan"
      ],
      "Resource": [
        "arn:aws:dynamodb:*:*:table/external-dns"
      ]
    }
```

The region and account ID may be specified explicitly specified instead of using wildcards. 

## Caching

The DynamoDB registry can optionally cache DNS records read from the provider. This can mitigate
rate limits imposed by the provider.

Caching is enabled by specifying a cache duration with the `--txt-cache-interval` flag.

## Migration from TXT registry

If any ownership TXT records exist for the configured owner, the DynamoDB registry will migrate
the metadata therein to the DynamoDB table. If any such TXT records exist, any previous values for
`--txt-prefix`, `--txt-suffix`, `--txt-wildcard-replacement`, and `--txt-encrypt-aes-key`
must be supplied.

If TXT records are in the set of managed record types specified by `--managed-record-types`,
it will then delete the ownership TXT records on a subsequent reconciliation. 