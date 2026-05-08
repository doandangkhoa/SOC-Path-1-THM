---
title: Advance Elastic Search
updated: 2026-03-12 15:23:20Z
created: 2026-03-12 06:06:01Z
latitude: 21.02776440
longitude: 105.83415980
altitude: 0.0000
---

# <span style="color: rgb(241, 196, 15);">Introduction to Advance Queries</span>

## Different Systaxes

Kibana supports <span style="color: rgb(241, 196, 15);">2 types</span> of syntax languages for querying in Kibana: `KQL` (Kibana Query Language) and `Lucene Query Syntax`

- KQL : user-friendly query language
- Lucene Query Syntax:
    - powered by an open-source search engine libary used as a backend for search engine (including ElasticSearch).
    - more powerful but harder.

## Special Characters

reserved characters in ELK include `+`, `-`, `=`, `&&`, `||`, `&`, `|` and `!`, you have to use backslash `\` previously. (e.g. `\+` ).

## Wildcards

used to filter data in ELK, match specific characters within a field value.

For example:

- `*`: match any characters, e.g. `product_name:monit*`
- `?`: match a single character, e.g.`name:J?n`

# Fuzzy Searches

is beneficial when searching for documents with inconsistencies or typos in the data,

systax: `field_name:search_term~fuzziness_value`

e.g. `host_name:server01~1` --> return :

```json
{
  {"host_name": "server01","status": "online"},
  {"host_name": "serber01","status": "online"}
}
```

eg: host_name:server_01~2 --> return:

```json
{
  { "host_name": "server01", "status": "online" }, 
  { "host_name": "serber01", "status": "online" },
  { "host_name": "sorvor01", "status": "online" }
}
```

**Note**: fuzzy searching doesn't work on nested data and only matches on one-word strings.

## Practice

**Including the misspellings, how many incidents has JLim handled where he misspelt the word “true”?**

`team_members.name:"JLim" AND incident_comments:true~4`

# **Proximity Searches (tìm kiếm theo khoảng cách)**

used to look for terms in proximate distance specified by slop value.

**Systax:** `field_name:"search term"~slop_value`

![1d52d6b939453e06b8dd9a6082bcb968.png](../../_resources/1d52d6b939453e06b8dd9a6082bcb968.png)

**Query:**

`log_message:"server error"~1`

**return:**

```json
{ "log_id": 1, "log_message": "Server error: failed login attempt." }, { "log_id": 4, "log_message": "Server: Detected error in connection." }
```

**Query:**

`log_message:"server error"~4`

**Return:**

<img src="../../_resources/de3336c9ed7339a684116f4739a4847d.png" alt="de3336c9ed7339a684116f4739a4847d.png" width="854" height="583" class="jop-noMdConv">

**Query:**

`log_message:"server error"~1 OR "login server"~1`

<img src="../../_resources/1254800003232d6b550858f51db5f008.png" alt="1254800003232d6b550858f51db5f008.png" width="855" height="669" class="jop-noMdConv">

## Practice

**How many incidents has AJohnston investigated that have the words "detected" and "negative" in the comments that are two words apart?**

`incident_comments:"detected negative"~2 AND team_members.name:"AJohnston"`

**How many incidents are there when you want to look for the words "data leak" and "true negative" in the comments that are at least 3 words in between them?**

`incident_comments:"data leak true negative"~3`

# Regular Expression

`Event_Type:/.*/`

`/.../` : báo hiệu bên trong là 1 biểu thức chính quy

`.`: match với bất kể kí tự nào

`*`: lặp lại . từ 0 -> n

<img src="../../_resources/4b4defc653af9a157906567761a5842d.png" alt="4b4defc653af9a157906567761a5842d.png" width="730" height="606" class="jop-noMdConv">

`Event_Type:/(S|M).*/`: tương tự như trên nhưng với kí hiệu đầu tiên là S hoặc M

<img src="../../_resources/895bc7c3bd0e9c335e13967c1f37ed3a.png" alt="895bc7c3bd0e9c335e13967c1f37ed3a.png" width="728" height="649">

## Practice

**How many incidents are there where a "client_list" file was affected by ransomware?**

`affected_systems.affected_files.file_name:/client_list.*/ AND incident_type:"Ransomware"`

**What is the name of the affected system at the earliest incident date that EVenis investigated with a filename containing the word "project"?**

`team_members.name:"EVenis" AND affected_systems.affected_files.file_name:/project.*/`

&nbsp;

&nbsp;