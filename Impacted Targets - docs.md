HEADERS:

Content-Type: application/json,

x-api-token: <organization API token\>



BODY: {

repo: {

host: "github.com",

owner: <repo owner\>, <!--- For example, "trunk-io" --->

name: <repo name\>, <!--- For example, "merge-action" --->

},

pr: {

number: <pr number\>, <!-- For example, 5 -->

sha: <pr sha\>, <!-- For example, "07fc773f16c0353bc3820fed65d89afddb9f81c3" -->

},

targetBranch: <merge instance branch\>, <!-- For example, "main" -->

impactedTargets: \["target-1", "target-2", ...\] OR "ALL" <!--- see notes on "ALL" below --->

}