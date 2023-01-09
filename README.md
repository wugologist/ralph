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

### Karma
Ralph can track numerical scores of arbitrary strings

#### Adding/Removing Karma
> @ralph woody++
> > woody leveled up! (total: 10)

> @ralph tinny--
> > tinny took a hit! (total: -5)

This message type increments or decrements a karma count. Karma can be tracked for any string which does not contain
whitespace.

#### Leaderboard
> @ralph karma best
> > Best all time: <br> 1. @ralph (12) <br> 2. woody(10) <br> [...]

> @ralph karma worst
> > Worst all time: <br> 1. @tana (-10) <br> 2. tinny(-5) <br> [...]

This message type lists the top or bottom 10 karma scores.

#### Listing Labels
> @ralph who is @michael
> > @michael is up to something, testing on prod, regretting everything, [...]

This message type lists all labels for a user as a comma-separated list, ordered from oldest to newest.

## Setup
Ralph is designed to be hosted on AWS as a Lambda with supporting DynamoDB table.

### Lambda
All files in the `src/` directory are meant to be used as a Lambda function with the handler found in 
`lambda_function.lambda_handler`. The following environment variables are required:

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
An app manifest file is provided to help configure the Slack app. Ralph requires the following scopes:

| Scope               | Reason                                               |
|---------------------|------------------------------------------------------|
| `app_mentions:read` | Listen to messages containing at-mentions of the app |
| `chat:write`        | Send messages                                        |
| `reactions_write`   | Add emoji reactions to messages                      |

## Contributing

Good luck.
