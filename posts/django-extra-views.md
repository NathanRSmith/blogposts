---
title: 'django-extra-views for Django FormSets'
author: Nathan Smith
tags: [Django, cbv, web, formset]
---

In any web framework, forms can get complicated.  In the most basic case the developer is forced to manually check, parse, validate and use GET/POST parameters.  Many modern web frameworks abstract this into reusable field type with basic validation and parsing.  I am most familiar with Django, so that is what I will be discussing.

A basic form page in Django is easy to do:

* Define the Form or ModelForm subclass
* Specify the fields or use the automatic Model fields
* Add custom validation if needed.
* Use the form with a FormView class-based-view (cbv) to automatically handle passing POST/FILE parameters and trigger validation.
* Customize the template.
* Do something with the validated data.

This may seem like a lot of steps, but forms handle much of the common form handling tasks that would be a waste of time to reimplement for every form page.  Once the developer gets used to this pattern and familiar with the [FormView](http://ccbv.co.uk/projects/Django/1.6/django.views.generic.edit/FormView/) class, basic form handling is a breeze.

Eventually a client is going to want to edit multiple things at once.  It is fairly straightforward to hack FormView to handle 2 or even 3 forms, and often this is desired when the forms are different.  But what if the client wants to be able to edit an arbitrary amount of items simultaneously?  Enter [django-extra-views](https://github.com/AndrewIngram/django-extra-views) and, specifically, FormSetView and ModelFormSetView.

The [django-extra-views](https://github.com/AndrewIngram/django-extra-views) package is installable via pip as a regular python package.  It has no dependencies and works for Django 1.3 or higher.  Inside of the package are a number of incredibly useful cbv's and mixins that probably should be included in the Django core.

* FormSetView
* ModelFormSetView
* InlineFormSetView
* CreateWithInlinesView
* UpdateWithInlinesView
* GenericInlineFormSetView (for GenericForeignKeys)
* SortableListMixin
* SearchableListMixin

Back to the FormSet example, creating a FormSet view can be as simple as:

    class AddressFormSetView(FormSetView):
        form_class = AddressForm
        template_name = "address_formset.html"

or for a ModelFormSetView case:

    class AddressModelFormSetView(ModelFormSetView):
        model = Address
        form_class = AddressForm
        Template_name = "address_formset.html"

FormSets are handled here very similarly to the Django admin.  Each form is given a prefix which all rendered fields contain in order to identify which form they belong to.

    <input name="address_set-TOTAL_FORMS" type="hidden" value="2">
    <input name="address_set-INITIAL_FORMS" type="hidden" value="2">
    <input name="address_set-MAX_NUM_FORMS" type="hidden" value="1000">
    <input name="address_set-0-address" type="text">
    <input name="address_set-0-city" type="text">
    <input name="address_set-0-state" type="text">
    <input name="address_set-0-zip" type="text">
    <input name="address_set-1-address" type="text">
    <input name="address_set-1-city" type="text">
    <input name="address_set-1-state" type="text">
    <input name="address_set-1-zip" type="text">

For ModelFormSets add an `id` field to identify a model instance.  The form views accept option attributes for specifying the max number of forms allowed (`max_num`), the initial number of extra forms to include (`extra`), whether forms can be deleted (`can_delete`) which is more for ModelFormSetView and other normal FormView and TemplateView attributes.  Overall these views operate very similarly to FormView with similarly named methods to override if needed.  Formsets can get complicated, but these views make it simple.

Even more complicated than formsets are inline formsets.  An inline formset is a "master" form with one or more "child" formsets.  For example, a user profile with a list of editable previous addresses and a list of pseudonyms.  There is the "master" form for the user profile, a formset for the addresses and a formset for the pseudonyms.  Implementing this type of scenario with FormView would be a nightmare.  I know because I tried, and that is what lead me to find this package.

The complicated profile scenario is easily accomplished with InlineFormSetView, CreateWithInlinesView or UpdateWithInlinesView.  Probably UpdateWithInlinesView would be most appropriate in this case.

    class AddressInline(InlineFormSet):
        model = Address
        form_class = AddressForm


    class PseudonymInline(InlineFormSet):
        model = Pseudonym
        form_class = PseudonymForm
    

    class UpdateOrderView(UpdateWithInlinesView):
        model = Profile
        form_class = ProfileForm
        inlines = [AddressInline, PseudonymInline]

Boom.  The inline formset is now handled.  Ok, so it probably will not be quite this simple in the real world, but there are methods for overriding in each class if needed.  This will provide the Profile form and a list of inline formsets, each with a meta form and a list of forms for the formset.  Submitting is similar to the formset case, just make sure each form has the appropriate prefix and it will all get sorted out.

It should be noted that the documentation for django-extra-views is, unfortunately, rather lacking and the best way to get familiar with these classes is to read the source and try them out.  If I ever get free time and the urge to contribute, this package is definitely on my list of candidates.

To conclude, I highly recommend using this package if you ever need to handle formsets or their more complex siblings, inline formsets.  Yes, the documentation is not great, but the classes are fairly straightforward and make sense once you dive in.  I also hope that other useful views will be included in this package in the future to make it even more awesome.







