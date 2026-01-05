Do you know how when you're testing an API, you need a bunch of fake data (email addresses, names, telephone numbers), and you really don't wanna do it by hand, or copy/paste it from a lorem ipsum generator because we are lazy? Postman has a useful feature that does exactly this, and you may have even seen it without knowing it was an option: **random data variables**.

## What Are Random Variables in Postman?

Postman has a list of built-in dynamic variables, which will generate random values for you, on the fly. You can use them in your request body, headers, URL parameters, or pretty much anywhere you'd normally use a variable. They look like this:

{% raw %}
```text
{{$randomEmail}}
{{$randomFirstName}}
{{$randomPhoneNumber}}
{{$randomInt}}
{{$randomUUID}}
```
{% endraw %}

Each time you send a request, Postman replaces these with fresh, randomized values. Perfect for testing how your API handles different inputs.

## Example

Let’s say you’re testing a user registration endpoint. Instead of hardcoding values, you can do something like:

{% raw %}
```text
{
  "email": "{{$randomEmail}}",
  "name": "{{$randomFirstName}} {{$randomLastName}}",
  "phone": "{{$randomPhoneNumber}}"
}
```
{% endraw %}

Pretty neat, uh?

happy coding!
