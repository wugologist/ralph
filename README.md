# Ralph
This is a Slack conversion of the [LevelUp](https://www.thelevelup.com/) platform team's lovable
[Zulip](https://zulipchat.com/) chat bot, based on Ross Paffett's [original
implementation](https://github.com/raws/ralph).

## Usage
Ralph only listens to messages where it is at-mentioned. It performs several functions of questionable utility depending
on the contents of the message.

### Labeling Users
Ralph can remember labels which users apply to each other.

#### Adding a Label
> @ralph @michael is up to something

This message type stores a label for further reference. The contents of the message following the "is" is the label.
Duplicate labels for the same user are not permitted. The "is" may be substituted with `<<` if desired for
grammatical reasons. 

#### Removing a Label
> @ralph @michael is not up to something.

This message type removes a label from a user. If the contents of the message following "is not" exactly matches a label
for the user, that label is forgotten. The "is not" may be substituted with `>>` if desired for grammatical reasons. 

#### Listing Labels
> @ralph who is @michael
> > @michael is up to something, testing on prod, regretting everything, [...]

This message type lists all labels for a user as a comma-separated list, ordered from oldest to newest.

## Setup
Ralph is designed to be hosted on AWS as a Lambda with supporting DynamoDB table.

### Lambda
All files in the `src/` directory are meant to be used as a Lambda function with the handler found in 
`index.lambda_handler`. The following environment variables are required:

| Name                | Description                                              | Example         |
|---------------------|----------------------------------------------------------|-----------------|
| `DYNAMO_TABLE_NAME` | Name of the DynamoDB table to be used (see schema below) | `ralph`         |
| `LOG_LEVEL`         | Optional, default `WARN`                                 | `INFO`          |
| `REGION`            | AWS Region                                               | `us-east-1`     |
| `SLACK_API_TOKEN`   | The bot user OAuth token generated by Slack for your app | `xoxb-12345...` |

### DynamoDB
Ralph uses one DynamoDB table with the following schema:

|                   | Name   | Type   | Note                                     |
|-------------------|--------|--------|------------------------------------------|
| **Partition Key** | type   | String | The type of message the item is used for |
| **Sort Key**      | target | String | e.g. the user ID relevant to the item    |

### Slack
Ralph's Slack app requires the following scopes:

| Scope               | Reason                                               |
|---------------------|------------------------------------------------------|
| `app_mentions:read` | Listen to messages containing at-mentions of the app |
| `chat:write`        | Send messages                                        |
| `reactions_write`   | Add emoji reactions to messages                      |

## Contributing

Good luck.