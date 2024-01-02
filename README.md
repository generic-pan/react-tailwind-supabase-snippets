# Supabase Snippets

# Setting Supabase Trigger for Each New User
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

# Allowing Supabase RLS - Each User Can Edit Their Own
```
create policy "User can manage their own subscriptions." on subscriptions for all to authenticated using (auth.uid () = account_id)
with
  check (auth.uid () = account_id);
```

# Connecting with client JS
```
const supabase = createClient('https://{{URL}}.supabase.co', '{{API_KEY}}');
```
Inserting
```
const { data, error } = await supabase
                .from('projects')
                .insert(
                {
                    name: leadObj.name,
                    deadline_date: (leadObj.deadline).toLocaleString('en-US'),
                    owner_id: (await supabase.auth.getUser()).data.user.id },
                )
```
Selecting
```
const { data, error } = await supabase
              .from('projects')
              .select("*")
              .eq('id', 'f83f2fh1')
```
