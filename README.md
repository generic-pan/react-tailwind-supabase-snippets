# react-tailwind-supabase-snippets

# Setting Supabase Trigger
```
create function public.handle_new_user()
returns trigger
language plpgsql
security definer set search_path = public
as $$
begin
  insert into public.{{NAME_OF_TABLE}} ({{ACCOUNT_ID_COLUMN}})
  values (new.id);
  return new;
end;
$$;

-- Trigger to execute the function after a new user is created
create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();
```
